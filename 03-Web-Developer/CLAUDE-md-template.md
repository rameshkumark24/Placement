# `CLAUDE.md` — the per-project rules file

> **What this is.** [AGENT-CONTEXT.md](AGENT-CONTEXT.md) is how I work *in general* — it lives in
> Notion and I paste it into a chat. **This** is the file that lives *in the repo*, committed at the
> root, so the agent reads it automatically every session without me pasting anything.
>
> ⭐⭐ **Both. Not one or the other.** The Notion file carries the rules between projects; this file
> carries the specifics of *this* project.

**Setup:** run `/init` to generate a first draft from the codebase, then replace it with the template
below and fill in the angle brackets. Commit it. Update it whenever a convention changes — a stale
`CLAUDE.md` is worse than none, because the agent follows it confidently.

---

## The template — copy from here

```md
# Project: <name>

## What this is
<One sentence: who uses it, and for what.>
<One sentence: what it must never do — lose an order, double-charge, expose a document.>

## Stack — do not substitute without asking
- <Next.js 15 App Router, TypeScript, Tailwind>
- UI: shadcn/ui as the base. reactbits.dev for motion pieces only.
- <Supabase (Postgres + Storage), Clerk (auth), Stripe (payments)>
- <Upstash Redis (rate limiting), Sentry (errors), Vercel (hosting)>
- Package manager: <pnpm>

## Folder structure
<paste your actual tree — 15 lines is enough>

## Commands
- Dev: <pnpm dev>
- Typecheck / lint / test: <pnpm typecheck && pnpm lint && pnpm test>
- Build: <pnpm build>

## Conventions
- Files kebab-case. Components PascalCase. Functions camelCase.
- Server Components by default; 'use client' only where interaction requires it,
  and pushed as far down the tree as possible.
- Server state via TanStack Query. Never hand-roll fetch-in-useEffect.
- All API input validated with Zod at the boundary.
- All errors returned as { error: { code, message } }.
- Dates stored UTC, formatted only at render.
- Money as integers in the smallest unit. Never floats.
- Every list has a loading, error, empty and success state. Empty is two
  states: never-had-any, and filtered-to-zero.

## Design rules
- shadcn components are owned and editable — edit them, don't wrap them in
  three layers.
- ONE accent colour: <#______>. Everything else is the neutral ramp.
- No gradient backgrounds. No glassmorphism. No emoji as icons — lucide,
  20px, stroke 1.5.
- Motion is deliberate: at most <one> reactbits effect per page, on the
  element that deserves attention.
- Never ship placeholder text. If you don't have copy, ask me for it.

## Security rules — non-negotiable
- Every DB query filters by the authenticated user's ID, server-side.
- Never put an authorization decision in the browser.
- RLS is ON and FORCED. The app filter and RLS are two layers, not one.
- Never spread req.body into an ORM call — whitelist fields explicitly.
- Never return a whole DB row. Return an explicit shape.
- Never add NEXT_PUBLIC_ to anything secret.
- Never use the service_role key in client code.
- Verify the Stripe webhook signature before processing any event.
- Never write PII to logs or Sentry.
- Uploads: validate magic bytes not extension, cap size, strip EXIF.

## API call rules — non-negotiable
- Every useEffect that fetches has a correct primitive dependency array.
- Every fetch gets an AbortController and a <10>s timeout.
- Retries: max 3, exponential backoff with jitter, never retry 4xx except 429.
- Debounce search inputs at 400ms, minimum 2 characters.
- Every new endpoint gets a rate limit before it is merged.
- Any agent/LLM loop has an explicit MAX_STEPS constant.
- Never introduce setInterval without a matching clearInterval.
- Payment operations carry an idempotency key.

## Do NOT
- Do not add a dependency without telling me the package name and why.
- Do not run migrations, `git push`, `git reset --hard`, or `git rebase`.
- Do not touch .env, CI config, or /infra without asking.
- Do not refactor files I did not ask you to change.
- Do not delete tests or weaken assertions to make a build pass.
- Do not invent endpoints — check <docs/api-contract.md> first.
- Do not claim something is tested if you did not run it.
- Do not agree with me if I ask for something that will leak data or
  cost money. Say so first.

## Plan first when
- The change touches auth, payments, or user data.
- The change spans more than three files.
- There is a schema change.
- The request is vague ("make it faster", "make it scale", "fix the design").

## Before you finish any task
1. Run <pnpm typecheck && pnpm lint && pnpm test>.
2. Tell me what changed and what I should look at.
3. Flag anything risky you noticed but did not change.
4. If it touched auth, data, payments or uploads — say so, so I run
   /security-review and a Codex cross-check.
```

---

## Keeping it honest

| ⭐ Rule | Why |
|---|---|
| **Update it when a convention changes** | ⭐⭐ A stale rule is followed confidently. That is worse than no rule. |
| **Keep it under ~120 lines** | It is read every session. Bloat costs you attention on the lines that matter. |
| **Write rules as commands, not preferences** | "Never spread req.body" beats "we prefer explicit fields" |
| **Put the *why* on the dangerous ones only** | A reason makes a rule stick; a reason on every line makes none of them stick |
| ⭐⭐ **Every rule here should have cost you something once** | If you cannot remember why a line is there, delete it |

---

**Back:** [folder index](README.md) · **The general rules:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md)
