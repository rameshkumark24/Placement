# Phase 08 — Testing & Review

Two separate jobs: **tests** prove the feature works, **review** finds what you didn't think to test.
Vibe-coded projects usually skip both, which is why they break in ways nobody can explain.

**Gate to pass this phase:** `/code-review` and `/security-review` return nothing you're unwilling
to ship, and an authorization test exists per resource.

---

## 1. The review gate

Run these before merging anything an agent wrote.

### `/code-review`

Reviews the current diff (or a PR number / branch / path) for correctness bugs plus
reuse, simplification and efficiency cleanups.

```
/code-review                 # current diff, reuses your last effort level
/code-review high            # broader coverage, may include uncertain findings
/code-review 42              # a specific PR number
/code-review --fix           # apply the findings to the working tree afterwards
/code-review --comment       # post findings as inline PR comments
/code-review ultra           # deep multi-agent cloud review of the branch
```

Effort levels: `low`/`medium` give fewer, high-confidence findings; `high`→`max` cast wider and may
surface uncertain ones. `ultra` runs a multi-agent review in the cloud — it's user-triggered and
billed, so use it on the branch that matters, not every commit.

### `/security-review`

A security review of the pending changes on the current branch. Run it whenever a change touches:

- authentication or session handling
- any database query
- an API route or server action
- file upload or user-supplied URLs
- payments or webhooks
- environment variables or configuration

### `/simplify`

Quality only — reuse, simplification, efficiency, altitude — and it applies the fixes. It does
**not** hunt for bugs. Use it after a feature works, not instead of `/code-review`.

### When to run what

| Situation | Run |
|---|---|
| Any agent-written diff | `/code-review` |
| Touched auth, queries, routes, payments, config | `/security-review` |
| Feature works but the code is sprawling | `/simplify` |
| Before merging a significant branch | `/code-review high` then `/security-review` |
| Before a launch, or on a payments/auth branch | `/code-review ultra` |

---

## 2. Cross-checking with a second agent

Different models miss different things. Running a second agent (Codex, or a different model) over
the same diff catches a real slice of what the first one glossed over — particularly the class of
bug where the author-agent is confident and wrong, because it re-reads its own code with the same
assumptions it wrote it with.

**How to use it well**

- Give it the diff and a **specific question**, not "find bugs". Vague prompts produce vague,
  invented findings.
- Ask for a ranked list with file and line references, so you can verify each one.
- **Verify every finding yourself before acting on it.** Second-opinion agents produce confident
  false positives at a meaningful rate — a finding is a hypothesis, not a fact.
- Good prompts:

> Review this diff for correctness bugs only. For each, give file:line, the input that triggers it,
> and the wrong behaviour. Rank by severity. Do not suggest style changes.

> This endpoint is meant to let a user read only their own notes. Show me every way a different
> user could read someone else's data.

> Find any code path where an API call could execute more times than intended — loops, missing
> dependency arrays, uncleaned intervals, unbounded retries.

**What it's bad at:** knowing your product intent. It will flag deliberate decisions as bugs.
Reading the finding takes ten seconds; acting on a wrong one costs an hour.

---

## 3. Tests that earn their place

Don't chase coverage. Write these:

### Authorization tests — the ones that matter most

```ts
test('user B cannot read user A note', async () => {
  const note = await createNote({ as: userA });
  const res = await fetch(`/api/notes/${note.id}`, { headers: authHeaders(userB) });
  expect([403, 404]).toContain(res.status);
});

test('user B cannot update user A note', async () => {
  const note = await createNote({ as: userA });
  const res = await fetch(`/api/notes/${note.id}`, {
    method: 'PATCH',
    headers: authHeaders(userB),
    body: JSON.stringify({ title: 'hijacked' }),
  });
  expect([403, 404]).toContain(res.status);
});

test('user cannot escalate their own role via mass assignment', async () => {
  const res = await fetch('/api/me', {
    method: 'PATCH',
    headers: authHeaders(userA),
    body: JSON.stringify({ name: 'A', role: 'admin' }),
  });
  const me = await getUser(userA);
  expect(me.role).toBe('user');
});
```

**One per resource, minimum.** This is the test suite that prevents the failure that actually kills
vibe-coded apps.

### The rest

- [ ] Unit tests on business logic and calculations
- [ ] Integration tests on API endpoints (Vitest + `supertest`/`fetch`)
- [ ] RLS policies tested against a real Supabase instance, with two accounts
- [ ] E2E on the **critical path only** (Playwright): signup → core action → payment
- [ ] Stripe webhook handler tested with replayed events, including duplicates
- [ ] External calls mocked (MSW) — tests must not hit the network
- [ ] A test DB per run, never the dev database

---

## 4. Edge cases agents never test

- [ ] Empty input, maximum length, unicode, emoji, SQL characters, HTML in text fields
- [ ] **Double-submit and double-click** on payment and create actions
- [ ] Concurrent submits of the same form
- [ ] Very large numbers, negative numbers, zero, `null`
- [ ] Expired session mid-action
- [ ] Slow network (throttle to 3G) and offline
- [ ] Browser back button mid-flow
- [ ] Every error state triggered deliberately and verified

---

## 5. Cross-browser, device & accessibility

- [ ] Chrome, Safari, Firefox
- [ ] **Real iOS Safari and real Android Chrome** — devtools emulation is not testing
- [ ] Breakpoints: 320–480 · 481–768 · 769–1024 · 1025+
- [ ] Mobile menu toggles, images scale, text readable, buttons tappable (44×44px), no horizontal scroll
- [ ] Keyboard-only navigation through the whole critical path
- [ ] Screen reader pass on the critical path
- [ ] Contrast verified (WCAG AA 4.5:1)
- [ ] Largest accessibility font size doesn't break layouts
- [ ] `prefers-reduced-motion` respected

Tools: [WAVE](https://wave.webaim.org) · axe DevTools · Lighthouse accessibility audit

---

## 6. UAT

- [ ] 3–5 real users, **watched silently** while they use it
- [ ] Don't explain anything — where they hesitate is your design bug
- [ ] Note every place they ask "what does this do?"
- [ ] Test with someone who has never seen the product

---

## 7. CI

Every PR, automatically:

```yaml
# .github/workflows/ci.yml
name: CI
on: [pull_request]
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: pnpm }
      - run: pnpm install --frozen-lockfile
      - run: pnpm typecheck
      - run: pnpm lint
      - run: pnpm test
      - run: pnpm build
      - run: pnpm audit --audit-level=high
```

- [ ] Branch protection requires this to pass before merge
- [ ] Dependabot PRs run the same pipeline

---

## Phase gate

- [ ] `/code-review` run and findings resolved
- [ ] `/security-review` run on anything touching auth, data, payments or config
- [ ] An authorization test exists per resource
- [ ] E2E covers the critical path
- [ ] CI green and required on `main`
- [ ] Real device testing done
