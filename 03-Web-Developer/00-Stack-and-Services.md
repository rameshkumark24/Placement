# Phase 00 — Stack & Services

The services, what each is actually for, and the configuration that matters. Set every one of these
up **before** writing code — retrofitting auth or rate limiting is far more expensive than wiring it
in on day one.

**Gate to pass this phase:** every account created, every spend cap set, every key in the right
environment.

---

## Setup order

Do it in this order — each step depends on the one above.

```
1. GitHub          repo + branch protection + secret scanning
2. Supabase        database + RLS + storage
3. Clerk           auth, wired to Supabase user IDs
4. Vercel          connected to GitHub, env vars set per environment
5. Cloudflare      DNS pointed at Vercel, WAF on
6. Upstash         Redis for rate limiting (before any public endpoint ships)
7. Sentry          errors + alert routing
8. Stripe          test mode first, live mode last
9. Pinecone        only if the app has semantic search / RAG
```

---

## GitHub — version control

The repo is the source of truth and the first line of defence.

- [ ] `.gitignore` covers `.env*`, `.vercel`, `node_modules`, build output — **before the first commit**
- [ ] `.env.example` committed with keys but no values
- [ ] **Branch protection on `main`**: require PR, require CI to pass, no force-push
- [ ] **Secret scanning + push protection enabled** (Settings → Code security). This blocks a commit
      that contains a key *before* it reaches the remote — the single highest-value free setting on
      this page.
- [ ] Dependabot alerts + security updates on
- [ ] GitHub Actions CI: typecheck, lint, test, build on every PR
- [ ] Repo private unless you have a reason otherwise

> **If a secret ever lands in a commit, rotate it.** Deleting the file does not help — it stays in
> history forever, and history is public the moment the repo is. Rotation is the only fix.

---

## Supabase — database

Postgres, plus storage, auth and realtime. You will use it mainly as Postgres + storage + **RLS**.

- [ ] Separate projects for dev and prod — never share a database between environments
- [ ] **RLS enabled on every table.** A table without RLS is readable by anyone with the anon key,
      and the anon key ships in your browser bundle.
- [ ] Policies tested with a second real account, not assumed
- [ ] `service_role` key used **only** in server code — never in a `NEXT_PUBLIC_*` var
- [ ] Connection pooling on (port 6543, the pooler) — serverless exhausts direct connections fast
- [ ] Daily backups on, and a restore actually performed once
- [ ] Point-in-time recovery if the data matters
- [ ] Files in Supabase Storage with its own RLS policies — never store files in table columns

```sql
-- The policy every user-owned table needs
alter table notes enable row level security;

create policy "owner reads own notes" on notes
  for select using (auth.uid() = user_id);

create policy "owner writes own notes" on notes
  for insert with check (auth.uid() = user_id);

create policy "owner updates own notes" on notes
  for update using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

> **RLS is not optional when using Clerk.** If Clerk holds identity and Supabase holds data, you
> must pass a verified user ID into Postgres (via a JWT template or a server-side client) and write
> policies against it. Otherwise every request arrives as "anonymous" and your policies either block
> everything or protect nothing.

---

## Clerk — authentication

Handles sessions, MFA, social login, organisations and roles. Do not hand-roll any of this.

- [ ] Middleware protecting routes, **plus a check inside each route handler** — middleware alone
      is not sufficient, it can be bypassed by direct API calls in some configurations
- [ ] Session lifetime short; refresh rotation on
- [ ] MFA available for accounts holding anything valuable
- [ ] Webhook (`user.created`, `user.deleted`) → sync to your own `users` table, **signature verified**
- [ ] Roles/permissions defined in Clerk, enforced **server-side** on every request
- [ ] Test keys in dev, live keys in prod, never mixed
- [ ] Account deletion flow that genuinely deletes downstream data too

```ts
// ✅ Route-level check — never rely on middleware alone
import { auth } from '@clerk/nextjs/server';

export async function GET(req: Request) {
  const { userId } = await auth();
  if (!userId) return new Response('Unauthorized', { status: 401 });
  // and still filter the query by userId — see Phase 06
}
```

---

## Vercel — hosting

- [ ] Connected to GitHub; preview deploy on every PR
- [ ] Environment variables set **separately** for Production / Preview / Development
- [ ] **No secret in a `NEXT_PUBLIC_*` variable** — those are compiled into the browser bundle
- [ ] Preview deploys use test keys, never production keys
- [ ] **Spend cap set** (Settings → Billing) with an alert below it
- [ ] Deployment protection on preview URLs if they touch real data
- [ ] Build fails on typecheck/lint errors — don't let it deploy broken code
- [ ] Function timeouts and memory set deliberately

> Preview deploys are public URLs by default. A preview pointing at your production database is a
> data leak with a friendly hostname.

---

## Cloudflare — DNS, WAF & edge

Sits in front of Vercel. Worth having from day one for DNS alone; the security features are free.

- [ ] DNS records pointed at Vercel, proxied (orange cloud) where appropriate
- [ ] SSL/TLS mode: **Full (strict)** — "Flexible" leaves traffic unencrypted behind the proxy
- [ ] Always Use HTTPS on; HSTS enabled once you're confident
- [ ] **WAF managed rules on** — blocks a large class of automated attacks before they reach you
- [ ] Bot Fight Mode on
- [ ] Rate limiting rules on `/api/auth/*` and any expensive endpoint (defence in depth alongside Upstash)
- [ ] **Block direct access to `/.env`, `/.git/*`, `/.svn/*`** — see [Phase 06](06-Security.md)
- [ ] Caching rules for static assets
- [ ] R2 for large file storage if egress costs bite (no egress fees, unlike S3)

---

## Upstash — Redis

Serverless Redis, billed per request. The rate-limiting layer that stops both abuse and your own
runaway loops.

- [ ] Rate limiting on every write, auth and expensive endpoint — **before** they ship publicly
- [ ] Caching for hot reads and expensive third-party responses
- [ ] Idempotency key storage for payment operations
- [ ] Queue (QStash) for anything slower than ~2 seconds
- [ ] Separate databases for dev and prod
- [ ] **Spend cap / budget alert set** — per-request billing means a loop bug bills you twice

```ts
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

export const authLimiter = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(5, '60 s'),
  prefix: 'rl:auth',
  analytics: true,
});
```

Suggested limits: auth `5/min` · writes `30/min` · reads `100/min` · AI calls `10/min`.

---

## Stripe — payments

- [ ] Test mode for everything until launch; live keys only in production
- [ ] **Webhook signature verified** on every event — an unverified webhook endpoint lets anyone
      mark orders as paid
- [ ] **Idempotency keys on every charge** — a network retry must not double-charge
- [ ] Webhook handler idempotent: store processed event IDs and ignore repeats
- [ ] Fulfilment driven by the webhook, **not** by the browser redirect (users close tabs)
- [ ] Amounts computed server-side from your own database — never trust a price sent by the client
- [ ] Currency and minor-unit handling correct (Stripe uses the smallest unit — paise for INR)
- [ ] Refund path tested
- [ ] Radar rules reviewed if you take card payments directly
- [ ] Tested live once with a real small charge, then refunded

```ts
// ✅ Webhook verification — the whole security model depends on this line
const event = stripe.webhooks.constructEvent(
  await req.text(),                         // raw body, not parsed JSON
  req.headers.get('stripe-signature')!,
  process.env.STRIPE_WEBHOOK_SECRET!,
);
```

> **India note:** for domestic recurring payments, RBI e-mandate rules apply. Razorpay handles
> Indian compliance and UPI more smoothly than Stripe — worth considering if your users are Indian.

---

## Sentry — errors, and the 3am call

Sentry decides whether you find out about an outage from a dashboard or from a user's tweet.

- [ ] SDK installed with **source maps uploaded** — a minified stack trace is useless
- [ ] Release tagging on, tied to the git SHA, so you know which deploy broke it
- [ ] User context attached (ID only — **never** email or PII in the event)
- [ ] **PII scrubbing on** — Sentry captures request bodies by default, which includes passwords
- [ ] Alert rules tuned: page on a *spike* or a new issue in a critical path, not on every event
- [ ] Alerts routed somewhere you'll actually see at 2am (phone, not a muted Slack channel)
- [ ] Performance monitoring on p95, not averages
- [ ] Cron/uptime monitoring on the real user-facing URL
- [ ] Noisy issues muted deliberately — an alert channel you ignore is worse than no alerts

> **Tune thresholds before launch, not after.** The most common failure is alert fatigue: 200
> notifications on day one, all muted by day three, and the real outage on day ten goes unseen.

---

## Pinecone — vector database

*Only if the app has semantic search or RAG. If not, skip — and if you do need vectors,
`pgvector` inside Supabase is free and usually enough until real scale.*

- [ ] **Namespace or metadata-filter per tenant/user** — this is the RAG equivalent of RLS
- [ ] Retrieval filtered by the requesting user's permissions **in the query**, not after
- [ ] Top-k bounded and a token budget set on what goes into the prompt
- [ ] Embedding costs modelled — re-embedding a whole corpus is a real bill
- [ ] Index dimensions matched to the embedding model, and both pinned

> **The RAG data leak:** embedding every document into one shared index and retrieving purely by
> similarity means user A's question can surface user B's document. Filter by `user_id` in the
> vector query itself.

---

## Cost model — do this now

Estimate the monthly bill at **10 / 1,000 / 10,000 users** before writing code.

| Service | Free tier ends when | Watch |
|---|---|---|
| Vercel | Bandwidth / function hours | Egress, image optimisation, function duration |
| Supabase | 500MB DB, 1GB storage | DB size, egress, connection count |
| Clerk | ~10k MAU | Monthly active users |
| Upstash | 10k commands/day | **Per-request billing — a loop bug bills you** |
| Sentry | 5k errors/month | An error loop burns the quota in an hour |
| Cloudflare | Generous | R2 storage, Workers requests |
| Pinecone | 1 starter index | Index size, query volume |
| Stripe | No free tier | 2–3% per transaction |

**Set a hard spend ceiling on every one of these, with an alert at 50% and 80%.** Serverless
scales your bill as happily as it scales your traffic.

---

## Environment variables

| Variable | Where it may appear |
|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Browser — fine |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Browser — fine **only if RLS is on every table** |
| `SUPABASE_SERVICE_ROLE_KEY` | Server only — **bypasses RLS entirely** |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | Browser — fine |
| `CLERK_SECRET_KEY` | Server only |
| `STRIPE_SECRET_KEY` | Server only |
| `STRIPE_WEBHOOK_SECRET` | Server only |
| `UPSTASH_REDIS_REST_TOKEN` | Server only |
| `PINECONE_API_KEY` | Server only |
| `SENTRY_AUTH_TOKEN` | Build time only |

**Verify before every launch:**

```bash
# Nothing secret should appear in the client bundle
grep -rE "service_role|sk_live|CLERK_SECRET|PINECONE_API" .next/static/ && echo "LEAK" || echo "clean"
```
