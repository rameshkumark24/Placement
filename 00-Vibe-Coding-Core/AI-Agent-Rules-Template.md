# 🤖 AI Agent Rules Template

Save as `CLAUDE.md` (Claude Code), `AGENTS.md` (Codex/generic), or `.cursorrules` (Cursor) in the
**repo root, in your first commit**. Without it the agent reinvents your stack every session and
contradicts its own earlier decisions.

Copy the block below, delete what doesn't apply, fill in the `<>` parts.

---

```md
# Project: <name>

## What this is
<One sentence: who uses it and for what.>

## Stack — do not substitute without asking
- Language: <TypeScript 5 / Python 3.12 / Java 21>
- Framework: <Next.js 15 App Router / FastAPI / Spring Boot 3>
- Database: <Postgres via Supabase>
- Auth: <Supabase Auth>
- Styling: <Tailwind + shadcn/ui>
- Hosting: <Vercel>
- Package manager: <pnpm>   # never use a different one

## Folder structure
<paste your actual tree; the agent will follow it>

## Conventions
- Files: kebab-case. Components: PascalCase. Functions: camelCase.
- One component per file, colocated with its test.
- Server state via TanStack Query. Never fetch-in-useEffect by hand.
- All API input validated with Zod at the boundary.
- All errors returned in the shape: { error: { code, message } }
- Dates stored UTC, formatted only at render.

## Security rules — non-negotiable
- Every DB query filters by the authenticated user's ID, server-side.
- Never put an authorization decision in the browser.
- Never spread req.body into an ORM call — whitelist fields explicitly.
- Never add a NEXT_PUBLIC_ prefix to anything secret.
- Never write PII to logs.

## API call rules — non-negotiable
- Every useEffect that fetches has a correct primitive dependency array.
- Every fetch gets an AbortController and a 10s timeout.
- Retries: max 3, exponential backoff with jitter, never retry 4xx except 429.
- Debounce search inputs at 400ms, minimum 2 characters.
- Every new endpoint gets a server-side rate limit before it is merged.
- Any agent/LLM loop has an explicit MAX_STEPS constant.
- Never introduce setInterval without a matching clearInterval.

## Do NOT
- Do not add a dependency without telling me the package name and why.
- Do not run migrations. Write them; I run them.
- Do not touch .env, CI config, or anything in /infra without asking.
- Do not refactor files I did not ask you to change.
- Do not delete tests to make a build pass.
- Do not invent API endpoints — check the API contract doc first.
- Do not write comments that restate the code.

## Before you finish any task
1. Run <pnpm typecheck && pnpm lint && pnpm test>.
2. Tell me which files you changed and why, in one line each.
3. Flag anything you were unsure about rather than guessing silently.
```

---

## How to keep it working

- **Update it when you correct the agent.** If you had to say "no, we use pnpm" once, that line
  belongs in the file — otherwise you'll say it again next session.
- **Keep it under ~150 lines.** Past that, the agent starts ignoring the middle.
- **Be specific, not aspirational.** "Write clean code" does nothing. "One component per file" works.
- **Put the do-nots last** — they're the part most likely to be followed if recent in context.
- Commit it. It's the highest-leverage file in a vibe-coded repo.

## Per-session ritual

```bash
git add -A && git commit -m "checkpoint before AI session"
```

Then open the session with context, not just a task:

> Read CLAUDE.md, docs/PRD.md and docs/api-contract.md. Then implement <task>.
> Do not touch anything outside <folder>. Show me the plan before you write code.
