# Phase 04 — Build

**Gate to pass this phase:** `CLAUDE.md` committed, a real device build running by day two, CI green.

---

## 1. Get onto a real device by day two

Simulators hide font rendering, keyboard behaviour, safe areas, performance, permission dialogs,
push notifications, biometrics, and every network condition that matters.

**An app that has only ever run in a simulator is not a tested app.**

- [ ] Development build on a **real iPhone** and a **real Android** in week one
- [ ] At least one **low-end Android** in the test set — that's what most users have
- [ ] EAS Build producing installable artifacts
- [ ] Expo Go used only for the earliest prototyping — it can't test native modules

---

## 2. Git discipline

```bash
git add -A && git commit -m "checkpoint before AI session"
```

- [ ] `git init` and first commit **before the first prompt**
- [ ] `.env` and `.gitignore` correct before the first commit
- [ ] Commit before every AI session
- [ ] One branch per feature
- [ ] Never let the agent run `git push`, `git reset --hard`, or `git rebase`
- [ ] Branch protection on `main`
- [ ] GitHub secret scanning + push protection on
- [ ] **Android keystore backed up** — or use Play App Signing. Lose it and you can never update
      the app; you must publish a new listing and lose every install and review.

> If a secret reaches a commit, **rotate it**. On mobile this is worse than web: the leaked key may
> already be inside binaries on user devices.

---

## 3. `CLAUDE.md`

```md
# Project: <name>

## What this is
<One sentence.>

## Stack — do not substitute without asking
- React Native + Expo SDK 52, TypeScript, Expo Router, NativeWind
- Supabase (Postgres + Storage), Clerk (auth), Upstash (rate limiting)
- TanStack Query for all server state, Zustand for local state
- Sentry for errors, EAS Build + EAS Update
- Package manager: pnpm

## Folder structure
<paste your actual tree>

## Conventions
- Files kebab-case. Components PascalCase.
- Server state via TanStack Query. Never hand-roll fetch-in-useEffect.
- All API input validated with Zod.
- Dates stored UTC, formatted only at render.
- Money as integers in the smallest unit. Never floats.
- Styling via NativeWind classes. No inline StyleSheet objects unless animating.

## Mobile rules — non-negotiable
- NEVER call an API from a render body or Flutter build(). Fetch in an effect or a query hook.
- Every listener (AppState, NetInfo, keyboard, location) is removed on unmount.
- useFocusEffect refetches are time-gated, never unconditional.
- Every setInterval has a matching clearInterval.
- Tokens go in expo-secure-store. NEVER AsyncStorage.
- No secret in an EXPO_PUBLIC_ variable — those are compiled into the binary.
- Every screen handles loading, empty, error and offline states.
- Every list uses FlashList with a bounded page size.
- Respect safe areas via react-native-safe-area-context. Never hardcode padding.
- Handle the Android back gesture on every screen.

## Security rules — non-negotiable
- Every DB query filters by the authenticated user's ID, server-side.
- Never put an authorization decision in the client. It is fully attacker-controlled.
- Never spread request bodies into an update — whitelist fields.
- Verify webhook signatures before processing.
- Never log PII.

## API call rules — non-negotiable
- Retries: max 3, exponential backoff with jitter, never retry 4xx except 429.
- Every request has a timeout and is cancelled on unmount.
- Every new endpoint gets an Upstash rate limit before it is merged.
- Any agent/LLM loop has an explicit MAX_STEPS constant.

## Do NOT
- Do not add a dependency without telling me the package name and why.
- Do not run migrations. Write them; I run them.
- Do not modify app.config.ts, eas.json, or native folders without asking.
- Do not refactor files I did not ask you to change.
- Do not delete tests to make a build pass.

## Before you finish any task
1. Run `pnpm typecheck && pnpm lint && pnpm test`.
2. List the files you changed and why, one line each.
3. Flag anything you were unsure about rather than guessing.
```

Keep it under ~150 lines. Update it whenever you correct the agent twice on the same thing.

---

## 4. The session loop

```
1. PLAN    — ask for a plan, no code. Read it. Correct it.
2. COMMIT  — checkpoint.
3. BUILD   — one feature, one branch.
4. REVIEW  — read every line.
5. VERIFY  — /code-review, then /security-review if it touched auth, data or payments.
6. TEST    — on a real device, not a simulator.
7. COMMIT  — real message.
```

---

## 5. Prompt patterns

### Starting a feature
> Read `CLAUDE.md` and `docs/api-contract.md`. I want to add \<feature>. **Do not write code yet** —
> plan: which files, what the data flow is, what happens offline, what could break. Under 20 lines.

### Mobile-specific review
> Review this screen for: API calls in the render body, listeners not removed on unmount,
> missing offline state, hardcoded padding instead of safe area insets, and touch targets under 44pt.

### Debugging
> Here's the error: \<full error + stack>. Before changing anything, give me the three most likely
> causes, ranked. Then fix only the most likely one.

Never "it doesn't work, fix it" — the agent rewrites unrelated working code.

---

## 6. Anti-patterns

| You say | What happens | Say instead |
|---|---|---|
| "Make it better" | Random rewrite | "Reduce duplication in `<file>` only" |
| "Fix all the bugs" | Invents bugs, changes 30 files | "Fix this error: \<paste>" |
| "Add tests" | Assertion-free noise | "Add one test proving user B can't read user A's note" |
| "Also while you're there…" | Unreviewable diff | Finish, commit, new task |
| 3-hour session | Context rot | Commit, restart with the rules file |

---

## 7. Dependency verification

**AI invents package names; attackers pre-register them (slopsquatting).**

- [ ] Package exists with that exact spelling, real weekly downloads, recent commits, real repo
- [ ] **Expo compatibility checked** — `npx expo install <pkg>`, not `pnpm add`. A package
      incompatible with your SDK version breaks the native build in ways that are painful to debug.
- [ ] Native modules verified as supported by EAS Build
- [ ] Lock file committed
- [ ] Bundle size impact considered — every dependency ships to every user's device

---

## 8. Build order

```
1. Schema + migrations + RLS      → verify account B can't read account A
2. Auth (Clerk) + SecureStore     → verify the session survives an app restart
3. API layer + Zod + rate limits  → verify with curl BEFORE any UI
4. Navigation shell               → empty screens, but routing works
5. Design system + components
6. Core feature screen
7. Offline + error states          → not an afterthought
8. Push notifications + deep links
9. Payments (Stripe or IAP)
10. Analytics + Sentry
11. Store assets + release
```

---

## 9. Engineering basics the agent will skip

- [ ] TypeScript strict; no `any` merged
- [ ] One error shape across endpoints
- [ ] Zod validation at every boundary
- [ ] Lint + format on a pre-commit hook
- [ ] `.env.example` committed
- [ ] EAS build profiles with separate env vars
- [ ] README with setup steps that work on a clean machine
- [ ] Dead code the agent left behind, deleted

---

## Phase gate

- [ ] `CLAUDE.md` committed
- [ ] Running on a real iPhone and a real low-end Android
- [ ] Branch protection + secret scanning on
- [ ] Keystore backed up / Play App Signing enrolled
- [ ] CI green on every PR
- [ ] Every dependency verified and Expo-compatible
