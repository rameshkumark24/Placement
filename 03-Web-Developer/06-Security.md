# Phase 06 — Security

**OWASP Top 10:2025** — #1 Broken Access Control (SSRF merged into it), #2 Security
Misconfiguration, #3 Software Supply Chain Failures. All three are exactly where AI-generated code
is weakest, because an agent optimises for "it works", not "it can't be abused".

**Gate to pass this phase:** the exposure check returns 404 on every path, the IDOR test returns
403, and no secret appears in the client bundle or git history.

---

## 0. The exposure check — do this on every deploy

Attackers scan every domain on the internet for these paths, automatically, within hours of a
domain going live. **Each one is a total compromise, not a partial one.**

```bash
DOMAIN=yourdomain.com

for path in \
  /.env /.env.local /.env.production /.env.backup \
  /.git/config /.git/HEAD /.git/logs/HEAD \
  /.svn/entries /.DS_Store \
  /config.json /credentials.json /backup.sql /dump.sql \
  /wp-config.php /phpinfo.php \
  /.aws/credentials /.ssh/id_rsa \
  /server-status /actuator/env /debug /__debug__
do
  code=$(curl -s -o /dev/null -w "%{http_code}" "https://$DOMAIN$path")
  [ "$code" = "404" ] || [ "$code" = "403" ] \
    && echo "ok   $code  $path" \
    || echo "LEAK $code  $path"
done
```

**Every line must be 404 or 403.** A `200` on any of them means stop and fix it now.

### Why `/.env` is catastrophic

It contains every key you have: `SUPABASE_SERVICE_ROLE_KEY` (bypasses all RLS — full database
read/write), `CLERK_SECRET_KEY` (impersonate any user), `STRIPE_SECRET_KEY` (issue refunds, read
customers), `UPSTASH_REDIS_REST_TOKEN`, `PINECONE_API_KEY`. One request, and the attacker owns
everything.

### Why `/.git/config` is worse than it looks

`/.git/config` returning 200 means the whole `.git` directory is served. An attacker runs a tool
like `git-dumper` and **reconstructs your entire repository — every file, every commit, every
branch, and every secret ever committed and later "removed"**. Deleting a key from the current
files never removed it from history.

```bash
# What an attacker runs the moment /.git/HEAD returns 200
git-dumper https://yourdomain.com/.git/ ./loot
```

### Why it happens

| Cause | Fix |
|---|---|
| Static host serving the project root | Serve only the build output directory |
| `.env` copied into a Docker image | `.dockerignore` must list `.env*` and `.git` |
| `.git` deployed via rsync/FTP | Exclude `.git` from the deploy, or deploy from CI only |
| Framework misconfigured to serve dotfiles | Explicit deny rule |
| A `public/` or `static/` folder with a stray config file | Audit what's in it |

> **On Vercel this is unlikely by default** — it serves build output, not your repo. But it becomes
> possible the moment you add a custom server, a rewrite rule, a proxy, or move to a VPS/container.
> Check anyway: the cost of checking is 10 seconds, the cost of missing it is everything.

### Block it at Cloudflare too (defence in depth)

WAF → Custom rule → Block:

```
(http.request.uri.path contains "/.env")
or (http.request.uri.path contains "/.git")
or (http.request.uri.path contains "/.svn")
or (http.request.uri.path contains "/.aws")
or (http.request.uri.path contains "/.ssh")
or (http.request.uri.path eq "/.DS_Store")
```

### If any of them returned 200

1. **Rotate every key immediately** — Supabase service role, Clerk secret, Stripe, Upstash,
   Pinecone, Sentry. Assume all are compromised.
2. Fix the serving configuration.
3. Re-run the check.
4. Review Supabase logs, Stripe events and Clerk sessions for unauthorised activity.
5. Only then go back to building.

---

## 1. Authentication (Clerk)

- [ ] Clerk middleware protecting routes, **plus a check inside every route handler** — middleware
      alone is not sufficient
- [ ] Session lifetime short; refresh rotation on
- [ ] MFA available for accounts holding anything valuable
- [ ] Clerk webhooks (`user.created`, `user.deleted`) **signature-verified**
- [ ] Roles defined in Clerk, enforced server-side on **every** request, not cached from login
- [ ] Test keys in dev, live keys in prod, never mixed
- [ ] Account deletion genuinely deletes downstream data
- [ ] No account enumeration — signup/login/reset return identical messages for existing and
      non-existing emails
- [ ] Login rate limited (Upstash + Cloudflare)

---

## 2. Authorization — the IDOR layer

**The section that matters most.** Broken access control is how vibe-coded apps leak data.

```ts
// 💀 The classic AI-generated IDOR — any user reads any note
const { data } = await supabase.from('notes').select('*').eq('id', params.id).single();
return Response.json(data);

// ✅ Ownership enforced in the query
const { userId } = await auth();
if (!userId) return new Response('Unauthorized', { status: 401 });

const { data } = await supabase
  .from('notes')
  .select('id, title, body')
  .eq('id', params.id)
  .eq('user_id', userId)          // ← the line that matters
  .single();

if (!data) return new Response('Not found', { status: 404 });
```

- [ ] **Every** query filters by the authenticated user's ID, server-side, in the query itself
- [ ] RLS enabled on every table and tested with a second account ([Phase 03](03-Architecture-and-Data.md#2-rls--write-it-now-not-later))
- [ ] Admin routes behind a separate server-side guard, not a hidden UI link
- [ ] **Mass assignment blocked** — whitelist updatable fields. `{...req.body}` into an update lets
      a user set `role: 'admin'` or `user_id: <someone else>`.
- [ ] No authorization decision made in the browser
- [ ] Ownership verified on update and delete, not just read
- [ ] Return **404, not 403**, for resources the user doesn't own — 403 confirms the resource exists

### The IDOR test — manually, before every launch

1. Create two accounts, A and B.
2. As A, create a resource. Note its ID from the URL or network tab.
3. Log in as B. Request A's resource ID directly.
4. **Expect 403/404. If you get the data, you have a data leak.**
5. Repeat for **update and delete**, not just read.
6. Repeat with the session cookie removed entirely.

---

## 3. Secrets

- [ ] `.env` in `.gitignore` **before** the first commit
- [ ] **No secret in a `NEXT_PUBLIC_*` variable** — those are compiled into the browser bundle
- [ ] `service_role` key never in client code — it bypasses RLS completely
- [ ] Secrets in Vercel env vars, per environment; preview deploys use test keys
- [ ] GitHub secret scanning + push protection on
- [ ] Secrets scanned across **git history**, not just current files
- [ ] **Any leaked key is rotated, not just deleted**

```bash
# Scan history
git log -p | grep -iE 'sk_live|sk_test|service_role|CLERK_SECRET|api[_-]?key|password' | head
gitleaks detect --source . -v

# Confirm the client bundle is clean
pnpm build
grep -rE "service_role|sk_live|CLERK_SECRET|PINECONE_API|UPSTASH_.*TOKEN" .next/static/ \
  && echo "🚨 LEAK" || echo "✅ clean"
```

---

## 4. Headers & transport

- [ ] HTTPS enforced; HSTS set
- [ ] Cloudflare SSL/TLS mode **Full (strict)** — "Flexible" leaves traffic unencrypted behind the proxy
- [ ] CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
- [ ] CORS restricted to known origins — **never `*`**
- [ ] Graded at [securityheaders.com](https://securityheaders.com)

```js
// next.config.js
const securityHeaders = [
  { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
  { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
];

module.exports = {
  async headers() {
    return [{ source: '/:path*', headers: securityHeaders }];
  },
};
```

---

## 5. Input & endpoints

- [ ] Zod validation at every API boundary, server-side
- [ ] Parameterised queries only — no string-concatenated SQL
- [ ] Output escaped; `dangerouslySetInnerHTML` avoided (sanitise with DOMPurify if unavoidable)
- [ ] CSRF protection on cookie-based sessions
- [ ] File uploads: type allowlist, size cap, sanitised filename, **served from a separate origin**
- [ ] Rate limiting per IP and per user on all write and auth endpoints
- [ ] **Stripe webhook signatures verified** — an unverified endpoint lets anyone mark orders paid
- [ ] Production errors carry no stack traces, SQL, or table names
- [ ] Debug endpoints, seed routes and default credentials removed before launch
- [ ] **SSRF**: any user-supplied URL validated against an allowlist before you fetch it — blocks
      `http://169.254.169.254` cloud-metadata attacks

---

## 6. Payments (Stripe)

- [ ] Webhook signature verified on **every** event, using the raw body
- [ ] Amounts computed server-side from your own database — never trust a client-sent price
- [ ] Idempotency keys on every charge
- [ ] Webhook handler idempotent — Stripe will deliver the same event twice
- [ ] Fulfilment driven by the webhook, not the browser redirect
- [ ] Live keys only in production
- [ ] Refund path tested

---

## 7. Supply chain

*AI hallucinates package names; attackers pre-register them — slopsquatting.*

- [ ] Every package the agent adds verified: exists, maintained, real download numbers, real repo
- [ ] Package names read character by character
- [ ] Lock file committed; no blind auto-upgrades
- [ ] `pnpm audit` + Dependabot on every build
- [ ] Licence check on every dependency
- [ ] Minimal dependency count

---

## 8. AI features (if any)

- [ ] Per-user quotas and a hard spend cap
- [ ] Model output **never** executed — no `eval`, no shell, no SQL interpolation
- [ ] Output escaped before display (models emit XSS payloads happily)
- [ ] Prompt injection handled — user content delimited, system prompt states it is data
- [ ] **Pinecone/RAG retrieval filtered by the requesting user's ID in the query** — otherwise
      semantic search is a cross-tenant data leak with extra steps
- [ ] PII not sent to third-party models without consent and a DPA
- [ ] The model never receives secrets or other users' data

---

## 9. AI-generated code review gate

Before merging anything an agent wrote, check each endpoint:

| Check | Why agents fail it |
|---|---|
| Auth check present | Forgotten on "internal" routes |
| Ownership check present | The IDOR default |
| Input validated server-side | Validated on the client and called done |
| No secrets inline | Example keys pasted, then made real |
| No unbounded `select('*')` | Performance and data exposure |
| Error messages generic | DB errors echoed to the user |
| Rate limit applied | Never added unless told |

Automate it: **`/security-review`** — see [Phase 08](08-Testing-and-Review.md).

---

## The 60-second pre-launch test

1. **Exposure check** — `/.env` and `/.git/config` return 404 ([§0](#0-the-exposure-check--do-this-on-every-deploy))
2. **IDOR test** — user B gets 403/404 on user A's resource
3. **Bundle check** — `grep -r "service_role" .next/static/` returns nothing
4. **Rate limit** — 50 requests to login in 10 seconds returns 429
5. **Error check** — a production 500 shows no stack trace, no SQL, no table names

---

## Tools

| Tool | Purpose |
|---|---|
| `gitleaks` / `trufflehog` | Secret scanning across git history |
| `git-dumper` | Verify your own `/.git` isn't exposed |
| `pnpm audit`, Dependabot, Snyk | Dependency vulnerabilities |
| OWASP ZAP | Automated web vulnerability scan |
| Burp Suite Community | Manual IDOR / auth testing |
| securityheaders.com | Header grade |
| Supabase RLS policy tests | Row-level security verification |
