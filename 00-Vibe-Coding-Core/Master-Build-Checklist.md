# 🧭 Master Build Checklist — Any Vibe-Coded App

Work top to bottom. **Each phase gates the next** — don't start Phase 4 until Phases 0–3 have
artefacts you can point at.

```
0. Scope → 1. Requirements → 2. Design → 3. Architecture → 4. Build → 5. Security
→ 6. Performance → 7. Testing → 8. Observability → 9. Reliability → 10. Launch → 11. Post-launch ↺
```

Legal & compliance (§12) runs in parallel from Phase 1 and must be live before public launch.

---

## 0️⃣ Scope & Feasibility

*Before a single document. This phase kills bad ideas cheaply.*

- [ ] Write the problem in one sentence — who hurts, and how much
- [ ] Define the single core job the app must do (if you can't pick one, scope isn't ready)
- [ ] Identify the target user, and one real person you can show it to
- [ ] Check what already exists — why does yours need to be built
- [ ] Define success metrics (signups, retention, revenue, time saved — pick 1–2)
- [ ] Draw the MVP line: v1 features vs. later. Write the "later" list down so it stops nagging
- [ ] Lock the tech stack and write down *why* each piece was chosen
- [ ] **Cost model** — estimate the monthly bill at 10 / 1,000 / 10,000 users
      (hosting, DB, storage, egress, email, LLM tokens)
- [ ] Set a hard monthly spend ceiling, and decide where the alert goes
- [ ] Check domain + app name availability
- [ ] List assumptions and risks — what would make this fail

> **Vibe-coding trap:** the agent will happily build v3 features in v1. The written "later" list is
> what you point at when it does.

---

## 1️⃣ Requirements & Documents

### Core documents

- [ ] **PRD** — problem, users, personas, user stories, features, out-of-scope, success metrics
- [ ] **TRD** — stack, architecture, integrations, constraints, non-functional requirements
      (load, latency, uptime targets)
- [ ] **App flow** — every page, every route, every transition; include error and empty paths
- [ ] **UI/UX brief** — brand, colour, type scale, spacing, component inventory, tone of voice
- [ ] **Backend schema** — tables, columns, types, relationships, constraints, ERD
- [ ] **Implementation plan** — milestones, task breakdown, sequence, dependencies, estimates

### The documents most people skip (and regret)

- [ ] **API contract** — every endpoint: method, path, auth required, request body, response shape,
      status codes, error format
- [ ] **Auth & roles matrix** — every role × every resource × create/read/update/delete.
      *This is the anti-IDOR document.*
- [ ] **Environment & config plan** — dev / staging / prod, every env var, where secrets live
- [ ] **Analytics & event tracking plan** — which events, which properties, which funnel they feed
- [ ] **Test plan** — what gets unit tested, what gets E2E tested, what "done" means

### Vibe-coding specific

- [ ] **`CLAUDE.md` / `AGENTS.md` / `.cursorrules`** written — stack, conventions, folder structure,
      do-nots, style rules. Template: [AI-Agent-Rules-Template.md](AI-Agent-Rules-Template.md)
- [ ] Feed the agent the PRD + schema + API contract as context *before* it writes anything
- [ ] Rule locked in up front: **never merge code you can't explain line by line**

---

## 2️⃣ UI/UX Design

- [ ] Wireframe every screen before styling anything
- [ ] Design system first: colour tokens, type scale, spacing scale, radius, shadows
- [ ] Component inventory — buttons, inputs, cards, modals, toasts
- [ ] Design all four states for every screen: **loading, empty, error, success**
- [ ] Onboarding / first-run experience — the empty state is the most-seen and least-designed screen
- [ ] Mobile layouts drawn, not assumed — mobile-first if your users are mobile
- [ ] Colour contrast meets WCAG AA (4.5:1 body text)
- [ ] Focus states visible for keyboard users
- [ ] Microcopy written: button labels, error messages, confirmation text
- [ ] Destructive actions get a confirmation; irreversible ones get a *typed* confirmation
- [ ] Validation messages are specific ("Password needs 8+ characters", not "Invalid")

---

## 3️⃣ Architecture & Data Model

- [ ] ERD finalised before any table is created
- [ ] Foreign keys defined with explicit cascade / restrict rules
- [ ] Constraints at the **database** level, not just the app (unique, not-null, check)
- [ ] Every table has `created_at` and `updated_at`
- [ ] All timestamps stored in UTC; convert at display time only
- [ ] Soft delete vs hard delete decided per table, and written down
- [ ] UUIDs vs sequential IDs decided — sequential IDs in URLs leak volume and invite enumeration
- [ ] Migrations versioned, reversible, committed to git
- [ ] Seed data script for local dev
- [ ] Files go to object storage (S3 / R2 / Supabase Storage), **never** into the database
- [ ] Multi-tenancy strategy decided if applicable (`tenant_id` on every row, enforced at the DB)
- [ ] Background job / queue strategy for anything slower than ~2 seconds

---

## 4️⃣ Development

### Vibe-coding discipline

- [ ] Git initialised **before** the first prompt
- [ ] Commit before every AI session — assume the agent may destroy working code
- [ ] One branch per feature; small, reviewable commits
- [ ] Agent never gets production database credentials or prod env access
- [ ] Read every generated migration before running it
- [ ] **Verify every new package** actually exists, is maintained, and has real download numbers
- [ ] Lock file committed; no blind auto-upgrades
- [ ] Delete dead code and unused files the agent leaves behind
- [ ] Review every AI-generated endpoint for: auth check present, ownership check present,
      input validated, no secrets inline

### Engineering basics

- [ ] `.env` in `.gitignore` **before** the first commit
- [ ] `.env.example` committed with keys but no values
- [ ] Typed end to end (TypeScript / Pydantic / equivalent)
- [ ] Consistent error handling — one shape for all API errors
- [ ] Input validation at the API boundary (Zod / Pydantic), server-side, always
- [ ] No business logic in the frontend that matters for security
- [ ] Linting + formatting on a commit hook
- [ ] README with local setup steps that actually work on a clean machine

---

## 5️⃣ Security Hardening

> Full detail: **[Security-Checklist.md](Security-Checklist.md)**

The headline: **Broken Access Control is #1 in the OWASP Top 10:2025** (SSRF was merged into it),
Security Misconfiguration is #2, and Software Supply Chain Failures is #3 — all three are exactly
where AI-generated apps are weakest.

Minimum gate to pass this phase:

- [ ] Every query filters by the authenticated user's ID, server-side
- [ ] Row Level Security enabled and tested with a second account
- [ ] IDOR tested manually: log in as A, swap an ID to B's resource, confirm 403
- [ ] Secrets scanned across **git history**, not just current files
- [ ] Rate limiting on all auth and write endpoints
- [ ] Security headers set (CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy)

---

## 6️⃣ Performance & Scalability

### Database

- [ ] Indexes on every foreign key and every column used in `WHERE` / `ORDER BY`
- [ ] `EXPLAIN ANALYZE` run on the five slowest queries
- [ ] **N+1 queries eliminated** — the single most common AI-generated performance bug
- [ ] Pagination on every list endpoint — never unbounded selects
- [ ] Cursor-based pagination instead of `OFFSET` for large tables
- [ ] Select only the columns you need
- [ ] **Connection pooling** configured (PgBouncer / Supabase pooler) — mandatory with serverless
- [ ] Tested with a realistic seeded dataset (100k+ rows), not 20 demo rows
- [ ] Archival / retention plan for tables that grow forever (logs, events, audit)

### Frontend & API

- [ ] Caching layer for hot reads (Redis, or framework-level cache)
- [ ] HTTP cache headers + CDN for static assets
- [ ] Images optimised, resized, lazy-loaded, modern formats
- [ ] Bundle analysed; code splitting and route-level lazy loading
- [ ] Compression enabled (gzip / brotli)
- [ ] Core Web Vitals measured — LCP, CLS, INP
- [ ] Skeletons / optimistic UI so waits feel shorter
- [ ] Search and autocomplete inputs debounced
- [ ] Slow work moved to background jobs, not held in the request
- [ ] Load test before launch (k6 / Artillery) at 10× expected peak
- [ ] You know where it breaks first — and what the fix is

---

## 7️⃣ Testing & QA

- [ ] Unit tests on business logic and calculations
- [ ] Integration tests on API endpoints
- [ ] **Authorization tests** — a second user must be blocked from the first user's data, in code
- [ ] E2E tests on the critical path only (Playwright / Cypress): signup → core action → payment
- [ ] Edge cases: empty, maximum length, unicode, emoji, SQL characters, concurrent submits
- [ ] Double-submit and double-click on payment and create actions
- [ ] Real device testing — iOS Safari and Android Chrome, not just devtools
- [ ] Cross-browser: Chrome, Safari, Firefox
- [ ] Accessibility pass — keyboard-only navigation, screen reader labels, contrast, focus order
- [ ] Slow network test (throttled to 3G) and offline behaviour
- [ ] Every error state triggered deliberately and verified
- [ ] UAT with 3–5 real users, watched silently while they use it
- [ ] Security test pass against the OWASP Top 10

---

## 8️⃣ Observability

- [ ] Error tracking wired up (Sentry) with source maps and release tagging
- [ ] Structured logs with request / correlation IDs
- [ ] Log redaction for passwords, tokens, PII
- [ ] Log retention period set (cost control + compliance)
- [ ] Uptime monitoring on the real user-facing URL
- [ ] `/health` endpoint that actually checks the DB, not just returns 200
- [ ] Performance monitoring — p95 latency, not just averages
- [ ] Product analytics (PostHog / GA4) firing the events from the tracking plan
- [ ] Dashboard for the business metrics defined in Phase 0
- [ ] Alerts routed somewhere you'll actually see at 2am
- [ ] Alert thresholds tuned — noisy alerts get ignored, which is worse than none
- [ ] **Billing / spend alerts** on hosting, DB and any AI APIs

---

## 9️⃣ Reliability & Recovery

- [ ] Automated daily backups enabled
- [ ] **Restore actually performed once** into a scratch environment — an untested backup is not a backup
- [ ] Point-in-time recovery enabled if the data matters
- [ ] RTO and RPO written down (how long down, how much data lost, acceptably)
- [ ] Backups stored separately from the primary DB, and encrypted
- [ ] Rollback procedure documented and rehearsed once
- [ ] Feature flags / kill switch for risky features — the cheapest form of rollback
- [ ] Retries with exponential backoff on third-party calls
      (bounded — see [API-Safety](API-Safety-and-Cost-Control.md#4-retry-storms))
- [ ] Idempotency keys on payments and any create operation that can be retried
- [ ] Graceful degradation when a third party is down — the app should bend, not break
- [ ] Staging environment that mirrors production
- [ ] Runbook: top five likely incidents and the first three steps for each

---

## 🔟 Pre-Launch & Deployment

- [ ] CI/CD pipeline: tests must pass before deploy
- [ ] Preview deploys on pull requests
- [ ] Dev / staging / prod fully separated — separate databases, separate keys
- [ ] Secrets in a manager (Vercel / Doppler / AWS SM), not in the repo or CI logs
- [ ] Migrations run as part of the deploy, reversibly
- [ ] Zero-downtime deploy verified
- [ ] Custom domain + SSL live; www / non-www redirect settled
- [ ] SEO basics: meta tags, OG image, sitemap, robots.txt, favicon
- [ ] 404 and 500 pages designed, not default
- [ ] Transactional email deliverability checked (SPF, DKIM, DMARC) — or reset emails go to spam
- [ ] Payment flow tested in live mode with a real small charge, then refunded
- [ ] Smoke test script run immediately post-deploy
- [ ] Soft launch to a small group before the public announcement
- [ ] Rollback command ready in a terminal during launch

---

## 1️⃣1️⃣ Post-Launch

- [ ] Support channel live and monitored (email, form, or chat)
- [ ] Feedback loop — somewhere users can complain that you actually read
- [ ] Watch error rate and latency hourly for the first 48 hours
- [ ] **Watch the bill daily for the first week**
- [ ] Funnel analytics reviewed — where do people drop off
- [ ] Changelog / release notes
- [ ] Dependency update cadence set (monthly)
- [ ] Tech debt log started — capture shortcuts while you still remember them
- [ ] Scaling triggers defined: at what number do you upgrade the DB tier
- [ ] Post-incident notes written after the first real incident
- [ ] Quarterly security review scheduled

---

## 1️⃣2️⃣ Legal & Compliance

*Runs parallel from Phase 1 — must be live before public launch.*

- [ ] Privacy Policy published and linked in the footer
- [ ] Terms & Conditions published
- [ ] Cookie consent if using analytics or third-party cookies
- [ ] **DPDP Act 2023 (India)** — consent notice, purpose limitation, grievance officer contact
      if handling personal data at scale
- [ ] GDPR obligations if any EU users — lawful basis, data export, right to erasure
- [ ] Account deletion flow that genuinely deletes (or documented anonymisation)
- [ ] Data export available to users
- [ ] Data retention policy written
- [ ] DPA reviewed for each vendor touching user data
- [ ] Refund policy if charging money
- [ ] Licence check on every dependency and template used
- [ ] Age restriction stated if applicable
- [ ] Data source licence read and redistribution rights confirmed in writing (if using external data)
