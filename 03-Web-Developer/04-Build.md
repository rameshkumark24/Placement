# Phase 04 — Build

How to work with the agent so the output is reviewable, and how to keep git as your undo button.

**Gate to pass this phase:** `CLAUDE.md` committed, CI green on every PR, no code merged that you
can't explain.

---

## 1. Git discipline — non-negotiable

```bash
git add -A && git commit -m "checkpoint before AI session"
```

- [ ] `git init` and first commit **before the first prompt**
- [ ] `.env` in `.gitignore` **before** the first commit
- [ ] Commit before every AI session — assume the agent may destroy working code
- [ ] One branch per feature; small, reviewable commits
- [ ] Never let the agent run `git push`, `git reset --hard`, or `git rebase`
- [ ] Branch protection on `main` — PR required, CI required, no force-push
- [ ] GitHub secret scanning + push protection on

> **If a secret reaches a commit, rotate it.** Removing the file does not remove it from history,
> and rewriting history on a pushed branch is worse than rotating one key.

---

## 2. The session loop

```
1. PLAN    — ask for a plan, no code. Read it. Correct it.
2. COMMIT  — checkpoint before the agent writes anything.
3. BUILD   — one feature, one branch, small scope.
4. REVIEW  — read every line. If you can't explain it, don't merge it.
5. VERIFY  — /code-review, then /security-review if it touched auth or data.
6. TEST    — make it prove the feature works, including the failure path.
7. COMMIT  — real message, real description.
```

Skipping step 1 is what produces 400-line diffs nobody understands.

---

## 3. `CLAUDE.md` — the highest-leverage file in the repo

Commit this at the root. Without it the agent reinvents your stack every session.

```md
# Project: <name>

## What this is
<One sentence: who uses it and for what.>

## Stack — do not substitute without asking
- Next.js 15 App Router, TypeScript, Tailwind + shadcn/ui
- Supabase (Postgres + Storage), Clerk (auth), Stripe (payments)
- Upstash Redis (rate limiting), Sentry (errors), Vercel (hosting)
- Package manager: pnpm

## Folder structure
<paste your actual tree>

## Conventions
- Files kebab-case. Components PascalCase. Functions camelCase.
- Server Components by default; 'use client' only where interaction requires it.
- Server state via TanStack Query. Never hand-roll fetch-in-useEffect.
- All API input validated with Zod at the boundary.
- All errors returned as { error: { code, message } }.
- Dates stored UTC, formatted only at render.
- Money as integers in the smallest unit. Never floats.

## Security rules — non-negotiable
- Every DB query filters by the authenticated user's ID, server-side.
- Never put an authorization decision in the browser.
- Never spread req.body into an ORM call — whitelist fields explicitly.
- Never add NEXT_PUBLIC_ to anything secret.
- Never use the Supabase service_role key in client code.
- Verify the Stripe webhook signature before processing any event.
- Never write PII to logs or Sentry.

## API call rules — non-negotiable
- Every useEffect that fetches has a correct primitive dependency array.
- Every fetch gets an AbortController and a 10s timeout.
- Retries: max 3, exponential backoff with jitter, never retry 4xx except 429.
- Debounce search inputs at 400ms, minimum 2 characters.
- Every new endpoint gets an Upstash rate limit before it is merged.
- Any agent/LLM loop has an explicit MAX_STEPS constant.
- Never introduce setInterval without a matching clearInterval.

## Do NOT
- Do not add a dependency without telling me the package name and why.
- Do not run migrations. Write them; I run them.
- Do not touch .env, CI config, or /infra without asking.
- Do not refactor files I did not ask you to change.
- Do not delete tests to make a build pass.
- Do not invent endpoints — check docs/api-contract.md first.

## Before you finish any task
1. Run `pnpm typecheck && pnpm lint && pnpm test`.
2. List the files you changed and why, one line each.
3. Flag anything you were unsure about rather than guessing silently.
```

**Keep it under ~150 lines** — past that the agent starts ignoring the middle. Update it every time
you have to correct the agent twice on the same thing.

---

## 4. Prompt patterns

### Starting a feature
> Read `CLAUDE.md` and `docs/api-contract.md`. I want to add \<feature>. **Do not write code yet** —
> give me a plan: which files you'd create or change, what the data flow is, and what could break.
> Under 20 lines.

### After the plan is agreed
> Implement step 1 only. Touch nothing outside `src/features/<x>`. List the files you changed and why.

### Making it explain itself
> Explain this function line by line, as if I have to defend it in a code review. Where would it
> break? What input would make it fail?

### Debugging
> Here's the error: \<full error + stack>. Before changing anything, give me the three most likely
> causes, ranked. Then fix only the most likely one.

Never say "it doesn't work, fix it" — the agent will rewrite unrelated working code.

### Dependency check
> List every dependency you added, with the exact package name and its weekly download count.
> Flag anything you are not certain exists.

---

## 5. Anti-patterns

| You say | What happens | Say instead |
|---|---|---|
| "Make it better" | Random rewrite of working code | "Reduce the duplication in `X.tsx` only" |
| "Fix all the bugs" | Invents bugs, changes 30 files | "Fix this error: \<paste>" |
| "Add tests" | 40 assertion-free tests | "Add one test proving user B can't read user A's note" |
| "Also while you're there…" | Unreviewable diff | Finish, commit, then a new task |
| Screenshot with no text | Agent guesses your intent | Say what's wrong *and* what you expected |
| 3-hour session | Context rot, contradictions | Commit, restart fresh with the rules file |

---

## 6. Engineering basics the agent will skip

- [ ] `.env.example` committed with keys but no values
- [ ] TypeScript strict mode on; no `any` in merged code
- [ ] One error shape across every endpoint
- [ ] Zod validation at every API boundary, server-side
- [ ] No business logic in the frontend that matters for security
- [ ] Lint + format on a pre-commit hook (husky + lint-staged)
- [ ] README with setup steps that work on a clean machine
- [ ] Dead code and unused files the agent left behind, deleted

---

## 7. Dependency verification

**AI invents package names. Attackers pre-register them — this is called slopsquatting.**

For every package the agent adds:

- [ ] Does it exist on npm, with the exact spelling? (`reqeusts` vs `requests`, `crossenv` vs `cross-env`)
- [ ] Weekly downloads in a plausible range for its purpose
- [ ] Recent commits, real maintainer, a linked repository
- [ ] Licence acceptable
- [ ] Lock file committed; no blind auto-upgrades

Every dependency is attack surface you didn't write and can't review.

---

## 8. Build order

Each layer testable before the next depends on it.

```
1. Schema + migrations + RLS   → verify account B can't read account A
2. Auth (Clerk)                → verify two accounts exist and sessions work
3. API routes + Zod + limits   → verify with curl BEFORE any UI
4. Design system + shell       → tokens, header, footer
5. Core feature UI             → the one job the app exists to do
6. Secondary pages             → settings, profile, admin
7. Payments (Stripe)           → test mode, webhook verified
8. Landing page                → last; it changes most often
9. Analytics + Sentry
10. SEO, legal pages, launch
```

**Why UI comes after API:** build UI first and the agent invents endpoint shapes, then you spend the
rest of the project reconciling them.

---

## Phase gate

- [ ] `CLAUDE.md` committed
- [ ] Branch protection + secret scanning on
- [ ] CI running typecheck, lint, test on every PR
- [ ] Every dependency verified
- [ ] Nothing merged you can't explain line by line
