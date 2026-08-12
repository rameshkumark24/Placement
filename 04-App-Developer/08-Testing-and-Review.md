# Phase 08 — Testing & Review

**Gate to pass this phase:** `/code-review` and `/security-review` clean, the real-device matrix
completed, and an authorization test per resource.

Mobile raises the stakes: a bug you ship takes 1–3 days to fix through review, and some users never
update. **Testing is not optional here in the way it can be on web.**

---

## 1. The review gate

Run these before merging anything an agent wrote.

### `/code-review`

Reviews the current diff (or a PR / branch / path) for correctness bugs plus reuse, simplification
and efficiency cleanups.

```
/code-review                 # current diff, reuses your last effort level
/code-review high            # broader coverage, may include uncertain findings
/code-review 42              # a specific PR number
/code-review --fix           # apply findings to the working tree afterwards
/code-review --comment       # post findings as inline PR comments
/code-review ultra           # deep multi-agent cloud review of the branch
```

`low`/`medium` give fewer, high-confidence findings; `high`→`max` cast wider. `ultra` is a
multi-agent cloud review — user-triggered and billed, so save it for the branch that matters.

### `/security-review`

Security review of the pending changes on the current branch. Run it whenever a change touches:

- authentication, session or token storage
- any database query
- an API route
- deep links or WebViews
- file upload or user-supplied URLs
- payments, IAP receipt validation, or webhooks
- permissions or native configuration

### `/simplify`

Quality only — reuse, simplification, efficiency — and it applies the fixes. It does **not** hunt
for bugs. Use it after a feature works, not instead of `/code-review`.

### When to run what

| Situation | Run |
|---|---|
| Any agent-written diff | `/code-review` |
| Touched auth, queries, payments, deep links, config | `/security-review` |
| Feature works but the code sprawls | `/simplify` |
| Before merging a significant branch | `/code-review high` then `/security-review` |
| **Before any store submission** | `/code-review ultra` + `/security-review` |

> Run the security review before **every** release, not just risky ones. A web mistake is fixed in
> minutes; this one ships to devices.

---

## 2. Cross-checking with a second agent

Different models miss different things. Running a second agent (Codex, or a different model) over
the same diff catches a real slice of what the first glossed over — especially the case where the
author-agent is confident and wrong, because it re-reads its own code with the assumptions it wrote
it with.

**How to use it well**

- Give it the diff and a **specific question**, not "find bugs". Vague prompts produce vague,
  invented findings.
- Ask for file:line references so you can verify each claim.
- **Verify every finding yourself before acting.** Second-opinion agents produce confident false
  positives at a meaningful rate — a finding is a hypothesis, not a fact.

Prompts that work:

> Review this diff for correctness bugs only. For each: file:line, the input that triggers it, and
> the wrong behaviour. Rank by severity. No style suggestions.

> Find every code path where an API call could fire more often than intended — calls in render
> bodies, missing dependency arrays, uncleaned listeners, unconditional focus refetches, unbounded
> retries.

> This screen must work offline. Show me every path where it would break, hang, or lose user data
> with no connectivity.

> This endpoint should let a user read only their own records. Show me every way another user could
> read someone else's data.

**What it's bad at:** knowing your product intent. It flags deliberate decisions as bugs.

---

## 3. Tests that earn their place

### Authorization tests — the ones that matter most

```ts
test('user B cannot read user A note', async () => {
  const note = await createNote({ as: userA });
  const res = await api.get(`/v1/notes/${note.id}`, { auth: userB });
  expect([403, 404]).toContain(res.status);
});

test('user B cannot update user A note', async () => {
  const note = await createNote({ as: userA });
  const res = await api.patch(`/v1/notes/${note.id}`, { title: 'x' }, { auth: userB });
  expect([403, 404]).toContain(res.status);
});

test('client cannot escalate role via mass assignment', async () => {
  await api.patch('/v1/me', { name: 'A', role: 'admin' }, { auth: userA });
  expect((await getUser(userA)).role).toBe('user');
});
```

**One per resource, minimum.**

### Offline & sync tests — mobile-specific

```ts
test('queued write is replayed exactly once on reconnect', async () => {
  await goOffline();
  await createNote({ title: 'offline note' });      // queued locally
  await goOnline();
  await waitForSync();
  const notes = await fetchNotes();
  expect(notes.filter(n => n.title === 'offline note')).toHaveLength(1);   // not 2
});
```

- [ ] Queued writes idempotent under replay
- [ ] Optimistic update rolls back visibly on failure
- [ ] Delta sync doesn't lose or duplicate rows
- [ ] Cache survives an app restart

### The rest

- [ ] Unit tests on business logic (Jest / Vitest)
- [ ] Component tests on complex interactive components (React Native Testing Library)
- [ ] API integration tests
- [ ] RLS policies tested against real Supabase with two accounts
- [ ] E2E on the **critical path only** (Maestro or Detox): launch → signup → core action → payment
- [ ] IAP / Stripe flows tested in sandbox, including restore-purchases
- [ ] Webhook handlers tested with duplicate deliveries

**Maestro** is usually the right choice for RN E2E — YAML flows, far less setup than Detox.

---

## 4. The real-device matrix

**Simulators lie.** They don't reproduce font rendering, keyboard behaviour, safe areas,
performance, permissions, push, biometrics, or network conditions.

| Device | Why |
|---|---|
| Real iPhone (recent) | Baseline iOS |
| Real iPhone SE-class | Small screen layout |
| Real Android flagship | Baseline Android |
| **Real low-end Android** | **Performance truth — your median user** |
| Tablet | If you claim tablet support |

---

## 5. Conditions to test

- [ ] **Slow 3G** and complete offline
- [ ] **Airplane mode toggled mid-request**
- [ ] App killed and relaunched mid-flow
- [ ] Incoming call during a payment
- [ ] Backgrounded for 30+ minutes, then resumed (OS may have killed it)
- [ ] Every permission **denied** path
- [ ] Permission granted, then revoked in system settings while the app runs
- [ ] Dark mode
- [ ] **Largest accessibility font size** — layouts must not break
- [ ] Landscape (or confirm it's locked)
- [ ] **Fresh install and upgrade-over-previous-version** — migration bugs live here
- [ ] Double-tap on every submit and payment button
- [ ] Deep link from a cold start
- [ ] Push notification tap: foregrounded, backgrounded, and fully killed
- [ ] Low storage and low battery mode
- [ ] Device date/time changed (breaks naive token expiry logic)

---

## 6. Accessibility

- [ ] VoiceOver (iOS) and TalkBack (Android) on the critical path
- [ ] Every icon-only button has an `accessibilityLabel`
- [ ] Dynamic font scaling at maximum, without broken layouts
- [ ] Contrast ≥ 4.5:1
- [ ] Touch targets ≥ 44pt / 48dp
- [ ] Reduce Motion honoured

---

## 7. Beta testing

- [ ] **TestFlight** (iOS) / **Play Internal Testing** (Android) with 3–5 real users
- [ ] Watched silently — where they hesitate is your design bug
- [ ] Feedback channel that isn't a store review
- [ ] Crash reports from beta triaged before submission
- [ ] Remember: a **new personal Google Play account needs a 14-day closed test with 12+ testers**
      before public release ([Phase 00](00-Stack-and-Services.md#store-accounts--do-this-first))

---

## 8. CI

```yaml
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
      - run: npx expo-doctor          # catches SDK/native incompatibilities early
      - run: pnpm audit --audit-level=high
```

- [ ] Branch protection requires this before merge
- [ ] EAS Build triggered on merge to `main` for a preview build

---

## Phase gate

- [ ] `/code-review` run, findings resolved
- [ ] `/security-review` run — mandatory before submission
- [ ] Authorization test per resource
- [ ] Offline/sync tests passing
- [ ] E2E on the critical path
- [ ] Full real-device matrix completed
- [ ] Upgrade-over-previous-version tested
- [ ] Beta round done with real users
