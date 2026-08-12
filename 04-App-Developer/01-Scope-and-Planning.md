# Phase 01 — Scope & Planning

**Gate to pass this phase:** PRD, **versioned** API contract and auth matrix written; offline
behaviour decided per screen.

---

## 1. Scope & feasibility

- [ ] Problem in one sentence — who hurts, and how much
- [ ] The **single core job** the app must do
- [ ] Target user identified, plus one real person you can hand the phone to
- [ ] Why this needs to be an app rather than a website — if there's no good answer, build the
      website. Apple rejects "minimum functionality" apps that are websites in a shell (guideline 4.2).
- [ ] Success metrics defined (installs, D7 retention, core action completion — pick 1–2)
- [ ] **Decided: iOS, Android, or both at launch.** Both doubles your testing surface.
- [ ] Minimum OS versions chosen
- [ ] MVP line drawn; the "later" list written down
- [ ] Cost model at 10 / 1,000 / 10,000 users ([Phase 00](00-Stack-and-Services.md#cost-model))
- [ ] Spend ceilings set with alerts
- [ ] **Payment category decided** — physical goods vs digital goods changes the whole payment
      architecture ([Phase 00](00-Stack-and-Services.md#payments--decide-this-before-building))
- [ ] App name checked on both stores; bundle ID chosen (**permanent**)

---

## 2. The documents

### Core six
- [ ] **PRD** — problem, users, personas, user stories, features, out-of-scope, metrics
- [ ] **TRD** — stack, architecture, integrations, constraints, offline strategy
- [ ] **App flow** — every screen, every route, every transition, including error and empty paths
- [ ] **UI/UX brief** — brand, colour, type scale, spacing, component inventory, platform conventions
- [ ] **Backend schema** — tables, columns, types, relationships, ERD
- [ ] **Implementation plan** — milestones, sequence, dependencies

### The five most people skip
- [ ] **API contract — versioned from day one.** See below.
- [ ] **Auth & roles matrix** — role × resource × CRUD. The anti-IDOR document.
- [ ] **Environment & config plan** — dev / preview / prod, every var, per build profile
- [ ] **Analytics & event tracking plan**
- [ ] **Test plan** — including the real-device matrix

### API versioning is not optional on mobile

Users run old app versions for **months**. Some never update.

- [ ] Every endpoint under a version prefix (`/v1/…`) from the first commit
- [ ] **Never make a breaking change to an endpoint an old build still calls** — add `/v2` and
      keep `/v1` alive
- [ ] Deprecation policy written: how long old versions are supported
- [ ] **Forced-update mechanism designed now** — an endpoint the app checks on launch that can say
      "this version is no longer supported". You will need it eventually, and you cannot add it
      retroactively to already-released builds.

---

## 3. Offline strategy — decide per screen

Web apps can assume connectivity. Apps cannot. For every screen, pick one:

| Mode | Meaning |
|---|---|
| **Works offline** | Reads from local cache, writes queue and sync later |
| **Read-only offline** | Shows cached data, writes disabled with a clear message |
| **Blocked offline** | Requires connectivity, shows an explicit offline state |

- [ ] Every screen assigned a mode, written down
- [ ] Queued writes designed to be **idempotent** — they will be replayed
- [ ] Conflict resolution chosen (last-write-wins is fine, but decide deliberately)

---

## 4. Screen inventory

### Auth flow
- [ ] Splash / launch screen
- [ ] Onboarding carousel (skippable — never force it)
- [ ] Login · signup · forgot password · reset
- [ ] OTP / email verification
- [ ] Biometric unlock
- [ ] **Permission priming screens** — explain *before* the OS dialog appears

### Core app
- [ ] Home / feed — with a designed empty state
- [ ] Detail screen
- [ ] Create / edit form
- [ ] Search + filters
- [ ] Profile
- [ ] Settings — notifications, theme, language, **account deletion**
- [ ] Notifications list
- [ ] Paywall / subscription screen if monetised

### System states — every screen needs these
- [ ] Loading (skeleton, not a spinner)
- [ ] Empty
- [ ] Error, with a retry button
- [ ] **Offline banner** — web doesn't need this; mobile always does
- [ ] Pull-to-refresh
- [ ] Pagination / infinite scroll with a bounded page size

### Commerce
- [ ] Product list · detail · cart · checkout · order tracking
- [ ] Native payment sheet (**not** a WebView)
- [ ] Restore purchases (required by Apple if using IAP)

---

## 5. Permissions plan

List every permission the app will request, and for each:

- [ ] Why it's needed, in one sentence a reviewer would accept
- [ ] The **priming screen** copy that precedes the OS dialog
- [ ] What the app does when it's **denied** — every permission needs a graceful path
- [ ] The iOS purpose string (`NSCameraUsageDescription` etc.) — vague ones get rejected

> A denied permission is often permanent. Priming — explaining the value before asking — is the
> difference between 30% and 70% acceptance.

**Only request what you actually use.** Unused permissions cause store rejections.

---

## Phase gate

- [ ] `docs/PRD.md`, `docs/api-contract.md` (versioned), `docs/auth-matrix.md` exist
- [ ] Offline mode assigned to every screen
- [ ] Payment category decided
- [ ] Permissions listed with priming copy and denied paths
- [ ] Forced-update mechanism designed
- [ ] Store accounts registered ([Phase 00](00-Stack-and-Services.md#store-accounts--do-this-first))
