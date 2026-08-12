# 🌐 Web Developer — Complete Domain

Everything needed to take a web app from idea to live and keep it alive. Self-contained: nothing
here depends on another folder.

**Work the phases in order.** Each one gates the next — don't start Phase 4 until Phases 0–3 have
artefacts you can point at.

---

## Phases

| # | Phase | Gate to pass |
|---|-------|--------------|
| [00](00-Stack-and-Services.md) | **Stack & Services** | Every account created, spend cap set on each |
| [01](01-Scope-and-Planning.md) | **Scope & Planning** | PRD, API contract and auth matrix written |
| [02](02-Design-System.md) | **Design System** | Tokens defined, all four screen states designed |
| [03](03-Architecture-and-Data.md) | **Architecture & Data** | ERD final, RLS policies written before any table is used |
| [04](04-Build.md) | **Build** | `CLAUDE.md` committed, git discipline in place |
| [05](05-API-Safety.md) | **API Safety** | Every call has a loop guard, retry cap and rate limit |
| [06](06-Security.md) | **Security** | IDOR test passed, `/.env` and `/.git/config` return 404 |
| [07](07-Performance.md) | **Performance** | Lighthouse ≥ 90, tested on 100k seeded rows |
| [08](08-Testing-and-Review.md) | **Testing & Review** | `/code-review` and `/security-review` clean |
| [09](09-Observability.md) | **Observability** | Sentry live, alerts routed, spend alerts on |
| [10](10-Deploy-and-Launch.md) | **Deploy & Launch** | Smoke test passed, rollback command ready |
| [11](11-Post-Launch.md) | **Post-Launch** | Support channel live, legal pages published |

**Reference:** [Component Libraries](Reference/Component-Libraries.md) · [API Notes](Reference/API-Notes.md)

---

## The stack

| Layer | Service | Role |
|---|---|---|
| Hosting | **Vercel** | Deploys, preview builds, edge functions |
| Database | **Supabase** | Postgres, storage, realtime, RLS |
| Auth | **Clerk** | Sessions, MFA, org/roles |
| Payments | **Stripe** | Checkout, subscriptions, webhooks |
| Errors | **Sentry** | The 3am call |
| Version control | **GitHub** | Repo, Actions CI, secret scanning, Dependabot |
| Edge / DNS | **Cloudflare** | DNS, WAF, CDN, bot protection, R2 |
| Redis | **Upstash** | Rate limiting, caching, queues |
| Vector DB | **Pinecone** | Semantic search / RAG, if the app has AI features |

Full setup order and per-service configuration: **[00-Stack-and-Services.md](00-Stack-and-Services.md)**

---

## The five rules

1. **Git before prompt.** Commit before every AI session — agents delete working code confidently.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist.** Client-side checks are decoration.
4. **Every API call needs a ceiling** — a loop guard, a retry cap, and a spend alert.
5. **Verify every package the agent adds.** AI invents names; attackers pre-register them.

---

## The three failures that cause the most damage

| Failure | Cause | Phase |
|---|---|---|
| Huge surprise bill | `useEffect` without a dependency array hammering an endpoint | [05](05-API-Safety.md#1-the-render-loop) |
| Total secret compromise | `/.env` or `/.git/config` publicly readable | [06](06-Security.md#0-the-exposure-check-do-this-on-every-deploy) |
| Cross-user data leak | No ownership filter in the query; RLS off | [06](06-Security.md#2-authorization--the-idor-layer) |

---

## Review gates

Before merging anything an agent wrote:

```
/code-review          # correctness bugs, simplification, efficiency
/security-review      # security review of pending changes on the branch
```

Details and how to use a second agent (Codex) as a cross-check: **[08-Testing-and-Review.md](08-Testing-and-Review.md)**
