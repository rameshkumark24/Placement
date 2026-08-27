# 🧱 Stack & Services — the setup, and the caps that stop a disaster

> ⭐⭐ **Set the spend cap on the day you create the account, not the day you launch.** Every service
> here can bill you without limit by default, and the failure mode of a vibe-coded app is a loop that
> runs all weekend.

**This is the default stack. If a project uses something else, keep every rule in this folder and
swap the code** — the rules are stack-independent, the snippets are not.

---

# 1 · The stack, and why each

| Layer | Default | ⭐ What it is actually for |
|---|---|---|
| **Hosting** | Vercel | Preview deploy per PR, one-click rollback |
| **Database** | Supabase (Postgres) | ⭐⭐ Real Postgres + **RLS**, which is your second security layer |
| **Auth** | Clerk | Sessions, MFA, orgs — ⭐ do not build auth yourself |
| **Payments** | Stripe | ⭐ Never touch card data yourself |
| **Cache / limits** | Upstash Redis | Rate limiting, hot reads, queues |
| **Errors** | Sentry | ⭐⭐ Without it your error rate is *unknown*, not zero |
| **Edge / DNS** | Cloudflare | WAF, CDN, bot protection |
| **Repo / CI** | GitHub | Actions, secret scanning, Dependabot |
| **UI** | shadcn/ui + [reactbits.dev](https://reactbits.dev/) | ⭐ Code you own, not a dependency you fight |
| **Vector DB** | pgvector first, Pinecone if needed | ⭐⭐ Start with the Postgres you already run |

---

# 2 · Setup order — this order matters

```
 ① ⭐ GITHUB          repo · .gitignore with .env · secret scanning ON ·
                     push protection ON · branch protection on main
 ② ⭐⭐ SPEND CAPS     BEFORE writing code. §3. This is the step people
                     skip and regret.
 ③ SUPABASE          project · ⭐ RLS ON BY DEFAULT · connection pooler
 ④ CLERK             app · providers · ⭐ session length decided
 ⑤ VERCEL            link repo · env vars per environment · preview deploys
 ⑥ ⭐ SENTRY          both client and server · ⭐⭐ TRIGGER A TEST ERROR
                     AND CONFIRM IT ARRIVES
 ⑦ UPSTASH           Redis · ⭐ rate limiter before the first public route
 ⑧ STRIPE            ⭐ TEST MODE FIRST · webhook endpoint · signature secret
 ⑨ CLOUDFLARE        DNS · proxy on · WAF · bot protection
 ⑩ ⭐⭐ CLAUDE.md      → [CLAUDE-md-template.md](CLAUDE-md-template.md)
```

---

# 3 · ⭐⭐ Spend caps — do this first

```
⭐⭐ EVERY ONE OF THESE CAN BILL YOU WITHOUT LIMIT BY DEFAULT.
   ⭐ A useEffect loop deployed on a Friday is a four-figure invoice on
     Monday, and the site looks completely fine the whole time.

  SET, FOR EACH SERVICE:
    · a HARD CAP  ⇒ what stops you being ruined
    · an ALERT BELOW IT ⇒ ⭐⭐ WHAT LETS YOU REACT. THIS IS THE POINT.
      Set it at ~50% of the cap.

  □ Vercel     — spend management, pause-at-limit ON
  □ Supabase   — spend cap; watch egress, it is the one that surprises
  □ Clerk      — MAU alert
  □ Upstash    — request cap
  □ ⭐⭐ ANY AI/LLM API — a monthly hard cap. The easiest to run away.
  □ Cloudflare R2 / storage — egress alert
  □ ⭐ YOUR CARD — a separate card or a virtual card with a limit for
     all dev services. ⭐⭐ THE LAST LINE THAT ACTUALLY WORKS.
```

**Write down the expected monthly cost per service now.** If the bill triples, you want to know what
"normal" was.

---

# 4 · Environment variables

```
⭐⭐ THE RULE: ANYTHING WITH NEXT_PUBLIC_ IS PUBLIC FOREVER.
   ⭐ It is inlined into the bundle as a literal string. View-source,
     Ctrl-F. And it is simultaneously in git history, every CDN cache
     and probably an archive.
   ⇒ ⭐⭐ IF ONE LEAKS, ROTATE IT. Deleting the code does nothing.

  ✅ NEXT_PUBLIC_ IS FINE FOR: the API URL, the Clerk publishable key,
     the Stripe PUBLISHABLE key (pk_), the Sentry DSN.
  ❌ NEVER: service_role, sk_live/sk_test, webhook secrets, any API key
     for a paid service, database URLs.

□ .env.local for dev, never committed
□ Different keys per environment. ⭐ Production keys never on your laptop.
□ ⭐ VALIDATE ENV AT STARTUP — fail loudly on boot rather than at 3am
   on the one code path that uses it.
□ ⭐⭐ GREP THE BUILD BEFORE EVERY DEPLOY:
     grep -rEn "sk_live|service_role|BEGIN PRIVATE" .next/ dist/
```

---

# 5 · Per-service settings that matter

```
SUPABASE
 □ ⭐⭐ RLS ON **AND FORCED** ON EVERY TABLE, from the first table
 □ ⭐ CONNECTION POOLER (transaction mode) — NOT optional on serverless.
    ⭐⭐ Without it you exhaust Postgres connections at trivial traffic
      and the whole site errors.
 □ Backups on; ⭐ RESTORE TESTED ONCE
 □ Storage buckets private by default; signed URLs for private files

CLERK
 □ Session length decided deliberately
 □ ⭐ MFA available on anything handling money or personal data
 □ Webhooks verified

STRIPE
 □ ⭐⭐ WEBHOOK SIGNATURE VERIFIED BEFORE PROCESSING ANYTHING
 □ ⭐ AMOUNTS COME FROM THE SERVER, never the client
 □ Idempotency key on every charge
 □ ⭐ TEST THE FAILURE CARDS: declined, 3DS required, insufficient funds

SENTRY
 □ Client AND server
 □ ⭐ SOURCE MAPS UPLOADED, NOT SERVED PUBLICLY
 □ ⭐⭐ PII SCRUBBED IN beforeSend — your error tracker becomes a data
    store you never designed, and usually the least-protected one
 □ Release/build id attached, so you can say "only Tuesday's deploy"

CLOUDFLARE
 □ Proxy ON (orange cloud) or the WAF does nothing
 □ ⭐ Always Use HTTPS · HSTS · minimum TLS 1.2
 □ Bot Fight Mode; rate limiting rules on auth routes

UPSTASH
 □ ⭐ RATE LIMITER WIRED BEFORE THE FIRST PUBLIC ROUTE SHIPS
 □ Per user AND per IP
```

---

# 6 · Swapping the stack

```
⭐⭐ THE RULES SURVIVE. THE SNIPPETS DO NOT.

  Supabase → Neon/RDS/PlanetScale  ⇒ ⭐ YOU NOW OWN THE RLS EQUIVALENT
                                      YOURSELF. The app-level filter is
                                      no longer the second layer — it
                                      is the ONLY layer.
  Clerk → Auth.js/Cognito          ⇒ ⭐ session, MFA and revocation are
                                      now your problem
  Vercel → Fly/Render/a VPS        ⇒ ⭐⭐ YOU NOW OWN: TLS, the SPA
                                      fallback for deep routes, cache
                                      headers, and rollback
  Stripe → anything               ⇒ ⭐ webhook signature verification
                                      and idempotency still apply
  Next.js → Vite SPA               ⇒ ⭐⭐ NO SERVER. Every "server-side"
                                      rule in this folder now needs a
                                      real backend somewhere.

⭐ WHEN YOU SWAP, WRITE THE NEW CHOICE INTO CLAUDE.md IMMEDIATELY, or
  the agent keeps generating code for the old stack.
```

---

**Back:** [folder index](README.md) · **Security:** [05-Security.md](05-Security.md) ·
**Per-project rules:** [CLAUDE-md-template.md](CLAUDE-md-template.md)
