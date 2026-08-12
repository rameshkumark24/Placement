# Phase 01 — Scope & Planning

Before a single prompt. This phase kills bad ideas cheaply and gives the agent something concrete
to work from — an agent without documents invents requirements and contradicts itself next session.

**Gate to pass this phase:** PRD, API contract and auth matrix written and saved in `docs/`.

---

## 1. Scope & feasibility

- [ ] Write the problem in one sentence — who hurts, and how much
- [ ] Define the **single core job** the app must do. If you can't pick one, scope isn't ready.
- [ ] Identify the target user, and one real person you can show it to
- [ ] Check what already exists — why does yours need to be built
- [ ] Define success metrics (signups, retention, revenue, time saved — pick 1–2)
- [ ] Draw the MVP line: v1 features vs. later. **Write the "later" list down** so it stops nagging —
      and so you have something to point at when the agent starts building v3 features
- [ ] Lock the tech stack and write down *why* each piece was chosen
- [ ] **Cost model** at 10 / 1,000 / 10,000 users ([Phase 00](00-Stack-and-Services.md#cost-model--do-this-now))
- [ ] Hard monthly spend ceiling set, and the alert routed somewhere you read
- [ ] Domain + app name availability checked
- [ ] Assumptions and risks listed — what would make this fail

---

## 2. The documents

Save these in `docs/`. They are the agent's context; feed them in before it writes anything.

### Core six

- [ ] **PRD** — problem, users, personas, user stories, features, out-of-scope, success metrics
- [ ] **TRD** — stack, architecture, integrations, constraints, non-functional requirements
      (load, latency, uptime targets)
- [ ] **App flow** — every page, every route, every transition. Include error and empty paths.
- [ ] **UI/UX brief** — brand, colour, type scale, spacing, component inventory, tone of voice
- [ ] **Backend schema** — tables, columns, types, relationships, constraints, ERD
- [ ] **Implementation plan** — milestones, task breakdown, sequence, dependencies, estimates

### The five most people skip and regret

- [ ] **API contract** — every endpoint: method, path, auth required, request body, response shape,
      status codes, error format. *A schema is not an API contract.*
- [ ] **Auth & roles matrix** — every role × every resource × create/read/update/delete.
      **This is the document that prevents IDOR.** Without it, authorization is decided ad hoc
      per endpoint, and one of them will be wrong.
- [ ] **Environment & config plan** — dev / preview / prod, every env var, where each secret lives
- [ ] **Analytics & event tracking plan** — which events, which properties, which funnel they feed
- [ ] **Test plan** — what gets unit tested, what gets E2E tested, what "done" means

### Auth matrix — the format

| Role | Resource | Create | Read | Update | Delete |
|---|---|---|---|---|---|
| anon | note | ✗ | ✗ | ✗ | ✗ |
| user | own note | ✓ | ✓ | ✓ | ✓ |
| user | others' note | ✗ | ✗ | ✗ | ✗ |
| admin | any note | ✓ | ✓ | ✓ | ✓ |

Every row becomes an RLS policy in [Phase 03](03-Architecture-and-Data.md) and a test in
[Phase 08](08-Testing-and-Review.md).

---

## 3. Page & route inventory

The input to your first build prompt. Tick what this project actually needs.

### Global chrome
**Header** — logo · nav · search · CTA · mobile hamburger · sticky behaviour
**Footer** — copyright · social · sitemap · contact · newsletter · **legal links**

### Landing page
- [ ] Hero — headline, subheadline, CTA, background media, trust indicators
- [ ] Social proof — logos, ratings, user count
- [ ] Features / services — cards, icon + description, benefit statements
- [ ] About — story, mission, values
- [ ] Testimonials — quotes, ratings, carousel, video
- [ ] Pricing — plan comparison, feature checklist, highlighted plan, monthly/yearly toggle
- [ ] Team — member cards, role, bio, socials
- [ ] Timeline / process — steps, milestones, roadmap
- [ ] Portfolio / gallery — grid, lightbox, category filter
- [ ] FAQ — accordion, searchable, categorised
- [ ] CTA band
- [ ] Contact — form, map, hours, email/phone

### App pages
- [ ] Login · signup · forgot password · reset password (Clerk handles most of this)
- [ ] Email verification
- [ ] Dashboard — **with a designed empty state**, the most-seen and least-designed screen
- [ ] Profile & account settings
- [ ] Billing / subscription management (Stripe customer portal)
- [ ] Admin panel — separate server-side guard, not a hidden link
- [ ] **Account deletion** — legally required in most jurisdictions
- [ ] 404 and 500 pages, designed

### E-commerce (if applicable)
- [ ] Product grid · cards · quick view
- [ ] Product detail — gallery/zoom, specs, variants, quantity, related
- [ ] Cart · wishlist · checkout · order tracking
- [ ] Search · category filter · sort · tags
- [ ] Reviews — display, submission, ratings, photos
- [ ] Promotions — banners, sale badges, countdown, promo codes

---

## 4. Every screen needs four states

**Loading · Empty · Error · Success**

Agents build the success state and skip the other three. Ask for all four explicitly, every time.

---

## Phase gate

You may start [Phase 02](02-Design-System.md) when:

- [ ] `docs/PRD.md` exists
- [ ] `docs/api-contract.md` exists
- [ ] `docs/auth-matrix.md` exists
- [ ] The "later" list is written down
- [ ] Spend ceilings are set on every service
