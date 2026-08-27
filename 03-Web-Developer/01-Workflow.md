# 🔁 Workflow — driving the agent

> ⭐⭐ **The output quality is set by the prompt and the review, not by the model.** Everything in
> this file is about those two ends. The middle — the agent writing code — is the part you do not
> control and should not try to.

---

# 1 · ⭐⭐ Plan mode — the highest-leverage habit

```
⭐⭐ THE FAILURE PLAN MODE PREVENTS:
   You ask for a feature. The agent makes six assumptions you would have
   rejected, writes 400 lines across nine files, and now reviewing it
   costs more than writing it would have.
   ⇒ ⭐ THE ASSUMPTIONS ARE THE PROBLEM. NOT THE CODE.

⭐ PLAN MODE MAKES THE ASSUMPTIONS VISIBLE BEFORE THEY BECOME CODE.
```

**When to use it — this is the judgement, not a rule:**

| Task | Mode | Why |
|---|---|---|
| Typo, padding, rename | ⭐ Just do it | Planning costs more than the change |
| One component, clear spec | ⭐ Do it, then explain | Small enough to review |
| **Auth, payments, user data** | ⭐⭐ **PLAN** | Wrong here is expensive and often silent |
| **More than ~3 files** | ⭐⭐ **PLAN** | You will not review 400 lines properly |
| **Schema change** | ⭐⭐ **PLAN** | Migrations are hard to undo |
| **"Make it faster / scale / look better"** | ⭐⭐ **PLAN** | Ambiguous — it *will* guess wrong |
| **A pattern that repeats across the codebase** | ⭐⭐ **PLAN** | Getting the shape wrong multiplies |

```
⭐⭐ HOW TO READ A PLAN — YOU ARE LOOKING FOR FIVE THINGS

  ① ⭐ WHAT IS IT ASSUMING that I did not say?
     ⇒ this is where most bad output starts
  ② ⭐⭐ WHAT IS IT TOUCHING that I did not ask about?
     ⇒ "and refactor the existing X while we're here" ⇒ NO
  ③ ⭐ WHAT IS IT NOT DOING? A good plan says so.
  ④ ⭐⭐ WHAT DO **I** HAVE TO DO? migrations, env vars, dashboard
     settings, DNS. If the plan does not say, ask.
  ⑤ ⭐ WHAT BREAKS IF THIS IS WRONG? If the answer is "money" or
     "data", slow down.

⭐⭐ DO NOT APPROVE A PLAN YOU DID NOT UNDERSTAND. Ask it to explain
  the step you skipped over. That question costs 30 seconds and has
  caught more bad work than any review tool.
```

---

# 2 · The session loop

```
 ① ⭐ PLAN     complex ⇒ plan mode, read it, correct it, approve it
 ② ⭐⭐ COMMIT  git add -A && git commit -m "checkpoint"
               ⭐ BEFORE THE AGENT WRITES ANYTHING. Agents delete
                 working code confidently.
 ③ BUILD      one feature, one branch, small scope
 ④ ⭐ EXPLAIN  "what changed, what should I look at, what worried you"
 ⑤ REVIEW     /code-review
 ⑥ SECURE     /security-review — if it touched auth, data, payments,
               uploads, or env
 ⑦ ⭐⭐ CROSS   Codex second opinion — money, auth, customer data (§4)
 ⑧ TEST       prove the feature AND prove the failure path
 ⑨ COMMIT     real message
```

**Git rules that are not negotiable:**

```
□ git init and first commit BEFORE the first prompt
□ .env in .gitignore BEFORE the first commit
□ commit before EVERY agent session
□ one branch per feature
□ ⭐⭐ THE AGENT NEVER RUNS: git push · git reset --hard · git rebase ·
   any migration
□ branch protection on main: PR required, CI required, no force-push
□ GitHub secret scanning + push protection ON

⭐ IF A SECRET REACHES A COMMIT — ROTATE IT. Deleting the file does not
  remove it from history, and rewriting a pushed branch is worse than
  rotating one key.
```

---

# 3 · ⭐⭐ Prompting — the four patterns that work

```
⭐⭐ ① GIVE IT THE CONSTRAINTS, NOT JUST THE GOAL.
   ❌ "Add a search box."
   ✅ "Add a search box to the orders list. Debounced 400ms, min 2 chars.
       Query goes in the URL so it survives refresh and is shareable.
       Empty result shows 'No orders match X' with a Clear button —
       not the same empty state as a new account.
       Do not add a dependency for this."

⭐⭐ ② TELL IT WHAT **NOT** TO DO. This changes the output more than
   anything else you can write.
   "Do not restyle anything else. Do not add a library. Do not touch
    the API. Do not change files outside src/components/search/."

⭐⭐ ③ ASK FOR THE FAILURE PATH EXPLICITLY. It will not volunteer it.
   "What happens if the request fails, times out, or returns zero rows?
    Build those three states too."

⭐⭐ ④ MAKE IT REPORT, NOT REASSURE.
   ❌ "Is this secure?"   ⇒ you will get "yes, it looks secure".
   ✅ "List every place this code trusts input from the browser.
       For each, say what happens if that input is hostile."
   ⭐ ASK FOR A LIST OF FACTS, NOT A VERDICT. A verdict is always
     optimistic; a list can be checked.
```

**The starting prompt for a new feature:**

> Read `CLAUDE.md`. I want to add \<feature\>. **Do not write code yet.**
> Tell me: which files change, what you are assuming, what you are *not* doing,
> what I have to do myself, and what breaks if this is wrong.

---

# 4 · ⭐⭐ The Codex cross-check — the final gate

```
⭐⭐ WHY: A MODEL REVIEWING ITS OWN CODE SHARES THE BLIND SPOT THAT
   PRODUCED THE BUG. A different model has different blind spots.
   ⇒ ⭐ THIS IS NOT ABOUT ONE BEING BETTER. It is about the OVERLAP
     being smaller than either one alone.

RUN IT WHEN THE DIFF TOUCHES:
   money · auth / authorization · customer data · file upload ·
   anything that sends email or SMS · a new public endpoint
```

```
⭐⭐ THE PROMPT — never ask "is this good?"

  "Here is a diff. Find bugs. Check specifically:
   ① Can a logged-in user reach another user's data by changing an ID?
   ② Can this loop, retry, or fire more than once? What does it cost
      if it does?
   ③ Can this be called without being logged in? Should it be?
   ④ What input breaks it — empty, null, huge, wrong type, negative,
      unicode, SQL characters?
   ⑤ What happens if the network fails halfway through?
   ⑥ What did the author assume that is not enforced anywhere?
   List concrete failures with line numbers. Do not summarise the code.
   If you find nothing, say so — do not invent findings."

⭐ THAT LAST LINE MATTERS. Without it you get invented findings, and
  then you cannot tell the real ones from the noise.
```

| ⭐ Outcome | What it means |
|---|---|
| Both models clean | ⭐ Reasonable confidence. Ship. |
| **They disagree** | ⭐⭐ **This is the valuable case — look yourself.** One is wrong and finding out which teaches you the codebase |
| Both find the same thing | ⭐ It is real. Fix it. |
| Second model finds something the first missed | ⭐⭐ Exactly why you ran it |

---

# 5 · Which tool for which job

| Job | Use | Note |
|---|---|---|
| **Complex or risky change** | ⭐⭐ **Plan mode** | The single highest-value habit |
| Correctness, simplification | `/code-review` | On every agent-written diff |
| Auth/data/payment/upload changed | `/security-review` | Not optional for these |
| Quality tidy-up, no bug hunt | `/simplify` | |
| **Final check on money/auth/data** | ⭐⭐ **Codex** | §4 |
| Searching an unfamiliar codebase | A search subagent | Cheaper than loading everything into context |
| Set up the repo's agent rules | `/init`, then edit | → [CLAUDE-md-template.md](CLAUDE-md-template.md) |
| A long repeating task | `/loop` | |
| **Architecture, subtle debugging** | ⭐ The strongest model | **Do not economise here** |
| Bulk mechanical edits | A faster model | Fine, and cheaper |

---

# 6 · ⭐⭐ Anti-patterns — what goes wrong and the fix

| ⭐ Symptom | Cause | Fix |
|---|---|---|
| A 400-line diff you cannot review | No plan, scope too big | ⭐⭐ Plan mode, and one feature per session |
| It changed files you did not mention | No boundary in the prompt | ⭐ "Do not touch anything outside \<dir\>" |
| It "fixed" the test instead of the bug | You asked for green, not correct | ⭐⭐ "Do not modify tests. Fix the code." |
| It added a library for something trivial | No constraint given | ⭐ "No new dependencies without asking" |
| Confidently wrong about an API | Hallucination | ⭐⭐ "If unsure it exists, say so and check the docs" |
| It agrees with your bad idea | You asked a leading question | ⭐ "Tell me why this is wrong before you build it" |
| The build passes, the page is blank | Error caught and swallowed | ⭐⭐ "Never catch an error without showing or logging it" |
| It rewrote working code | No instruction to leave it | ⭐ "Do not refactor anything I did not ask about" |

```
⭐⭐ THE ONE THAT COSTS THE MOST: "I'VE TESTED IT AND IT WORKS."
   ⭐ Agents say this without running anything.
   ⇒ ASK: "Did you actually run it? Paste the output."
   ⇒ AND PUT IT IN CLAUDE.md: "Do not claim something is tested if you
     did not run it."
```

---

# 7 · Reviewing what you cannot fully read

You are vibe coding. You will not read every line. **Read these instead:**

```
⭐⭐ THE SIX-POINT SKIM — 2 minutes, catches most of it

 ① ⭐ THE DIFF STAT. More files than you expected ⇒ stop and ask why.
 ② ⭐⭐ EVERY DATABASE QUERY. Does it filter by the current user?
 ③ ⭐ EVERY useEffect. Dependency array correct? Cleanup returned?
 ④ ⭐⭐ ANY NEW package.json LINE. Do you know that package? Is it real?
 ⑤ ⭐ ANY try/catch. Does the catch actually do something?
 ⑥ ⭐⭐ ANYTHING WITH process.env. Is a secret leaking client-side?

⭐ THEN ASK THE AGENT: "which part of this are you least confident
  about?" ⭐⭐ IT WILL USUALLY TELL YOU THE TRUTH, AND THAT IS WHERE
  THE BUG IS.
```

---

**Back:** [folder index](README.md) · **The memory:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md) ·
**Per-project:** [CLAUDE-md-template.md](CLAUDE-md-template.md)
