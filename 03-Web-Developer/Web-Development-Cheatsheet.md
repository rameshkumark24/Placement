# 🌐 Web Development — Complete Vibe-Coding Cheatsheet

Everything needed to take a web app from idea to live, built with an AI agent.

> Read [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/) first — the universal rules (security,
> API loops, agent discipline) are not repeated here. This file covers what is **web-specific**.

| Companion file | Contents |
|---|---|
| [UI-Component-Libraries.md](UI-Component-Libraries.md) | Curated component/animation libraries, fonts, assets |
| [Deploy-Analytics-SEO.md](Deploy-Analytics-SEO.md) | Vercel deploy, domain/DNS, Search Console, GA4, Lighthouse |
| [Reference/API-Notes.md](Reference/API-Notes.md) | Saved links |

---

## 📋 Contents

1. [Pick the stack](#1-pick-the-stack)
2. [Before you prompt](#2-before-you-prompt)
3. [Page & section inventory](#3-page--section-inventory)
4. [Build order that works](#4-build-order-that-works)
5. [Web-specific security](#5-web-specific-security)
6. [Web-specific API safety](#6-web-specific-api-safety)
7. [Performance & Core Web Vitals](#7-performance--core-web-vitals)
8. [Accessibility & responsive](#8-accessibility--responsive)
9. [Launch gate](#9-launch-gate)
10. [Maintenance rhythm](#10-maintenance-rhythm)

---

## 1. Pick the stack

Decide once, write it in `CLAUDE.md`, never let the agent substitute.

| Need | Default choice | Why |
|---|---|---|
| Framework | **Next.js 15 (App Router)** | Server components, API routes, best Vercel path |
| Simpler / static | Astro | Content sites, blogs, marketing — ships almost no JS |
| Styling | **Tailwind CSS** | Agents write it reliably; no CSS file drift |
| Components | **shadcn/ui** | Copy-in, not a dependency; you own the code |
| Database | **Supabase (Postgres)** | Postgres + auth + storage + RLS in one |
| Alternative DB | Neon + Drizzle | If you want the ORM explicit |
| Auth | **Supabase Auth** / Clerk | Never hand-roll auth |
| Server state | **TanStack Query** | Dedupes, caches, bounded retries — kills loop bugs |
| Forms | React Hook Form + Zod | One schema validates client and server |
| Payments | Razorpay (India) / Stripe | Both support idempotency keys |
| Email | Resend | Simple API, good deliverability |
| File storage | Supabase Storage / Cloudflare R2 | Never store files in the DB |
| Hosting | **Vercel** | Preview deploys per PR |
| Errors | Sentry | Source maps + release tagging |
| Analytics | PostHog or GA4 | Funnels vs. simple traffic |

**Cost reality check** — free tiers end. Estimate the monthly bill at 10 / 1,000 / 10,000 users
before you build, and set a spend cap on day one.

---

## 2. Before you prompt

- [ ] Problem written in one sentence
- [ ] Single core job of the app defined
- [ ] Sitemap + every route listed
- [ ] Colour tokens, type scale, spacing scale chosen
- [ ] Master prompt / PRD generated and saved to `docs/PRD.md`
- [ ] Backend schema + ERD drawn
- [ ] **API contract written** — method, path, auth, request, response, status codes
- [ ] **Auth & roles matrix written** — role × resource × CRUD
- [ ] `CLAUDE.md` in the repo ([template](../00-Vibe-Coding-Core/AI-Agent-Rules-Template.md))
- [ ] `git init` + first commit **before** the first prompt
- [ ] `.env` in `.gitignore`, `.env.example` committed
- [ ] Domain availability checked (Namecheap)

---

## 3. Page & section inventory

Tick what this project actually needs — this list is the input to your first build prompt.

### Global chrome

**Header** — logo · nav menu · search (optional) · CTA button · mobile hamburger · sticky behaviour
**Footer** — copyright · social links · sitemap links · contact · newsletter signup · legal links

### Landing page sections

- [ ] Hero — headline, subheadline, CTA, background image/video, trust indicators
- [ ] Social proof — logos, ratings, user count
- [ ] Features / services — cards, icon + description blocks, benefit statements
- [ ] About — story, mission, values
- [ ] Testimonials — quotes, star ratings, carousel, video
- [ ] Pricing table — plan comparison, feature checklist, highlighted plan, monthly/yearly toggle
- [ ] Team — member cards, role, bio, socials
- [ ] Timeline / process — steps, milestones, roadmap
- [ ] Portfolio / gallery — grid, lightbox, category filter
- [ ] FAQ — accordion, searchable, categorised
- [ ] CTA band — primary and secondary
- [ ] Contact — form, map, hours, email/phone

### App pages

- [ ] Login / signup / forgot password / reset password
- [ ] Email verification screen
- [ ] Dashboard (with a designed **empty state** — the most-seen screen)
- [ ] Profile & account settings
- [ ] Billing / subscription management
- [ ] Admin panel (separate server-side guard)
- [ ] 404 and 500 pages — designed, not default

### E-commerce (if applicable)

- [ ] Product grid, product cards, quick view
- [ ] Product detail — gallery/zoom, specs, variants, quantity, related products
- [ ] Cart, wishlist, checkout, order tracking
- [ ] Search, category filter, sort, tags
- [ ] Reviews — display, submission form, star ratings, customer photos
- [ ] Promotions — discount banners, sale badges, countdown, promo code input

### Every screen needs four states

**Loading · Empty · Error · Success.** Agents build the success state and skip the rest. Ask for
all four explicitly.

---

## 4. Build order that works

Building in this order means each layer is testable before the next depends on it.

```
1. Schema + migrations          → verify in the DB GUI
2. Auth (signup/login/reset)    → verify you can create 2 accounts
3. RLS policies                 → verify account B cannot read account A
4. API routes + validation      → verify with curl/Thunder, before any UI
5. Design system + layout       → tokens, header, footer, shell
6. Core feature UI              → the one job the app exists to do
7. Secondary pages              → settings, profile, admin
8. Marketing/landing page       → last; it changes most often
9. Analytics + error tracking
10. SEO, legal pages, launch
```

**Why UI comes after API:** if you build UI first, the agent invents endpoint shapes and you spend
the rest of the project reconciling them.

### Per-feature loop

```bash
git commit -m "checkpoint"        # before every AI session
# prompt: plan first, no code
# review plan → approve → implement one step
# read the diff line by line
pnpm typecheck && pnpm lint && pnpm test
git commit -m "feat: <specific thing>"
```

---

## 5. Web-specific security

> Full list: [Security-Checklist.md](../00-Vibe-Coding-Core/Security-Checklist.md)

Extra items that bite on the web specifically:

- [ ] **No secret in a `NEXT_PUBLIC_*` env var** — those are compiled into the browser bundle
- [ ] Search the production bundle for your service-role key before launch:
      `grep -r "service_role" .next/` — it must return nothing
- [ ] Server Components / server actions used for anything touching secrets — never a client component
- [ ] `dangerouslySetInnerHTML` avoided; if unavoidable, sanitise with DOMPurify
- [ ] CSRF protection on cookie sessions (Next.js server actions have it; custom routes may not)
- [ ] CORS restricted to your domain — not `*`
- [ ] Security headers set in `next.config.js` / middleware:
      CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy
- [ ] Verify the grade at [securityheaders.com](https://securityheaders.com)
- [ ] Rate limit on auth routes and any route that sends email or calls an LLM
- [ ] File uploads: type allowlist, size cap, sanitised filename, served from a separate origin
- [ ] Middleware auth checks cannot be the *only* check — the route itself must verify too

### The IDOR test (do this manually before every launch)

1. Create two accounts, A and B.
2. As A, create a resource. Note its ID from the URL or network tab.
3. Log in as B. Request A's resource ID directly.
4. **Expect 403/404. If you get the data, you have a data leak.**
5. Repeat for update and delete, not just read.

---

## 6. Web-specific API safety

> Full detail with code: [API-Safety-and-Cost-Control.md](../00-Vibe-Coding-Core/API-Safety-and-Cost-Control.md)

The web-specific traps, in the order they occur:

| Trap | Symptom | Fix |
|---|---|---|
| `useEffect` without deps | Endpoint hit thousands of times/min | Correct primitive dep array; enable `exhaustive-deps` as an **error** |
| Object literal in deps | Same as above, harder to spot | `useMemo` the object, or pass primitives |
| `setInterval` not cleared | Grows with every navigation | `clearInterval` in the cleanup return |
| No request cancellation | Stale responses overwrite fresh ones | `AbortController`, aborted in cleanup |
| Search on every keystroke | 6 requests for "laptop" | Debounce 400ms, min 2 chars |
| Unbounded retries | One outage becomes self-DDoS | Max 3, exponential backoff + jitter, never retry 4xx |
| No submit lock | Double-charged customers | Disable button while in flight + idempotency key |
| N+1 in a server component | Page takes 8s at 100 rows | Join / eager load |
| Unbounded list query | Works at 20 rows, dies at 20,000 | Server-enforced max page size |

**Use TanStack Query for all server state.** It dedupes identical in-flight requests, caches by key,
and gives bounded retries by default — it removes the entire top row of that table.

---

## 7. Performance & Core Web Vitals

Targets: **LCP < 2.5s · INP < 200ms · CLS < 0.1**

- [ ] `next/image` for every image — never a raw `<img>`
- [ ] Images in WebP/AVIF, correctly sized, lazy-loaded below the fold
- [ ] `next/font` for fonts (prevents layout shift and a render-blocking request)
- [ ] Explicit `width`/`height` on media so nothing shifts as it loads
- [ ] Bundle analysed (`@next/bundle-analyzer`); route-level code splitting
- [ ] Heavy client components dynamically imported
- [ ] Server Components by default; `'use client'` only where interaction requires it
- [ ] Indexes on every FK and every `WHERE`/`ORDER BY` column
- [ ] Connection pooling on (mandatory with serverless)
- [ ] Tested against 100k seeded rows, not 20 demo rows
- [ ] Compression (gzip/brotli) enabled
- [ ] CDN + cache headers on static assets
- [ ] Skeleton loaders / optimistic UI
- [ ] Lighthouse ≥ 90 on Performance, Accessibility, Best Practices, SEO
- [ ] Load tested at 10× expected peak (k6)

---

## 8. Accessibility & responsive

### Breakpoints to actually test

| Range | Device |
|---|---|
| 320–480px | Mobile |
| 481–768px | Tablet |
| 769–1024px | Laptop |
| 1025px+ | Desktop |

Verify at each: mobile menu toggles, images scale, text stays readable, buttons are tappable
(44×44px minimum), forms are usable, nothing overflows horizontally.

### Accessibility pass

- [ ] Colour contrast ≥ 4.5:1 for body text (WCAG AA)
- [ ] Full keyboard navigation — every interactive element reachable and operable
- [ ] Visible focus states (never `outline: none` without a replacement)
- [ ] Semantic HTML — real `<button>`, `<nav>`, `<main>`, heading hierarchy in order
- [ ] `alt` text on every meaningful image; empty `alt=""` on decorative ones
- [ ] Form labels tied to inputs; errors announced, not just coloured red
- [ ] Screen reader pass on the critical path
- [ ] Respects `prefers-reduced-motion`
- [ ] Tested on real iOS Safari and Android Chrome, not just devtools

---

## 9. Launch gate

> Deploy steps in detail: [Deploy-Analytics-SEO.md](Deploy-Analytics-SEO.md)

- [ ] CI passes: typecheck, lint, tests
- [ ] Dev / staging / prod separated — separate databases, separate keys
- [ ] Secrets in Vercel env vars, not in the repo
- [ ] Custom domain + SSL live; www/non-www redirect settled
- [ ] Meta tags, OG image, favicon, sitemap.xml, robots.txt
- [ ] 404 + 500 pages designed
- [ ] Email deliverability verified (SPF, DKIM, DMARC) — or reset emails go to spam
- [ ] Payment tested live with a real small charge, then refunded
- [ ] Sentry receiving events, with source maps
- [ ] Analytics firing the events from the tracking plan
- [ ] Uptime monitor on the real URL
- [ ] **Spend caps + billing alerts set**
- [ ] Privacy Policy + Terms published and linked in the footer
- [ ] Cookie consent if using analytics (DPDP Act 2023 / GDPR)
- [ ] Backup restore tested once into a scratch environment
- [ ] Rollback command ready in a terminal during launch
- [ ] Smoke test run immediately after deploy
- [ ] Soft launch to a small group first

---

## 10. Maintenance rhythm

**Weekly** — check analytics · review Search Console errors · test contact forms · verify backups ran
**Monthly** — update content · check broken links · review site speed · `npm audit` · dependency updates
**Quarterly** — full SEO audit · security review · UX review · competitor check · restore test

---

## Workflow summary

```
Plan → Document (PRD, schema, API contract) → CLAUDE.md → git init
  → Schema → Auth → RLS → API → UI → Landing
  → Security pass → Performance pass → A11y pass
  → Deploy (Vercel) → Domain → Search Console + Analytics
  → Spend caps → Soft launch → Monitor 48h → Iterate
```
