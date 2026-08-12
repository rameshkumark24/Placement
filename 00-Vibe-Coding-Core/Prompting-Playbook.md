# 🎯 Prompting Playbook

Vibe coding fails in predictable ways. This is the set of prompts and habits that prevent each one.

---

## The loop that works

```
1. PLAN    — ask for a plan, no code. Read it. Correct it.
2. COMMIT  — checkpoint git before the agent writes anything.
3. BUILD   — one feature, one branch, small scope.
4. REVIEW  — read every line. If you can't explain it, don't merge it.
5. TEST    — make it prove the feature works, including the failure path.
6. COMMIT  — small message, real description.
```

Skipping step 1 is what produces 400-line diffs you don't understand.

---

## Prompt patterns

### Starting a feature

> Read `CLAUDE.md` and `docs/api-contract.md`.
> I want to add <feature>. **Do not write code yet** — give me a plan: which files you'd create or
> change, what the data flow is, and what could break. Keep it under 20 lines.

### After the plan is agreed

> Implement step 1 only. Touch nothing outside `src/features/<x>`.
> When done, list the files you changed and why.

### Making it explain itself (the anti-slop prompt)

> Explain what this function does line by line, as if I have to defend it in a code review.
> Where would it break? What input would make it fail?

### Debugging

> Here's the error: <paste full error + stack>.
> Before changing anything, tell me the three most likely causes, ranked.
> Then fix only the most likely one.

Do **not** say "it doesn't work, fix it" — the agent will rewrite unrelated working code.

### Security review of generated code

> Review this endpoint against these five questions: is auth checked, is ownership checked, is
> input validated server-side, is there a rate limit, can any error leak internals?
> Answer each with yes/no and the line number.

### Before merging

> List every dependency you added, with the exact package name and its weekly download count.
> Flag anything you are not certain exists.

---

## Anti-patterns — what breaks a session

| You say | What happens | Say instead |
|---|---|---|
| "Make it better" | Random rewrite of working code | "Reduce the duplication in `X.tsx` only" |
| "Fix all the bugs" | Invents bugs, changes 30 files | "Fix this specific error: <paste>" |
| "Add tests" | 40 meaningless assertion-free tests | "Add one test that proves a user can't read another user's note" |
| "Also while you're there…" | Scope creep, unreviewable diff | Finish, commit, then new task |
| Pasting a screenshot with no text | Agent guesses your intent | Describe what's wrong *and* what you expected |
| Continuing a 3-hour session | Context rot, contradictions | Commit, start fresh with the rules file |

---

## Context hygiene

- **Start fresh sessions often.** Long sessions accumulate contradictions; the agent starts
  half-remembering decisions you reversed.
- Give it the *artefacts*, not a summary — the PRD, the schema, the API contract.
- If it invents an endpoint or column that doesn't exist, that's a signal the context is stale.
  Restart with the real files.
- When it apologises and tries the same fix twice, stop. Read the code yourself. The agent is
  guessing, and a third attempt will guess too.

---

## Reviewing AI code — the 5-minute pass

Read the diff asking only these:

1. Does this endpoint check **who is asking** and **whether they own this row**?
2. Is any user input reaching a query, a shell, or `dangerouslySetInnerHTML` unvalidated?
3. Is there a loop, interval, or effect that could fire far more often than intended?
4. Are these dependencies real, and did I want them?
5. Is there anything here I could not explain to someone else?

A "no" on #5 means don't merge, regardless of whether it works.

---

## Knowing when to stop vibe coding

Switch to reading docs and writing it yourself when:

- The same bug survives three fix attempts.
- The agent starts changing files you didn't mention.
- You're merging code you can't explain because the deadline is close.
- The feature touches payments, auth, or anything deleting user data.

Those four areas are where the cost of a subtle bug is far higher than the time you save.
