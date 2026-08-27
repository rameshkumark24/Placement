# 🧪 Testing & Review — including the Codex final check

> ⭐⭐ **You are not going to read every line the agent writes. So the review has to be structural,
> not line-by-line** — a small number of checks that catch the failures that actually happen, plus a
> second model looking with different blind spots.

---

# 1 · ⭐⭐ The review order

```
 ① ⭐ THE SIX-POINT SKIM (§2)        — 2 minutes, you, by eye
 ② /code-review                     — correctness, simplification
 ③ /security-review                 — if auth, data, payments, uploads
                                       or env changed
 ④ ⭐⭐ CODEX CROSS-CHECK (§3)        — money, auth, customer data
 ⑤ THE TESTS THAT MATTER (§4)
 ⑥ ⭐ REAL DEVICE + REAL NETWORK (§5)
 ⑦ ⭐⭐ THE SHIP CHECKLIST            — [10-Ship-Checklist.md](10-Ship-Checklist.md)
```

---

# 2 · ⭐ The six-point skim

```
⭐⭐ TWO MINUTES. THIS CATCHES MOST OF WHAT MATTERS IN A DIFF YOU
   CANNOT FULLY READ.

 ① ⭐ THE DIFF STAT. More files than you expected? Stop. Ask why.
 ② ⭐⭐ EVERY DATABASE QUERY — does it filter by the current user?
 ③ ⭐ EVERY useEffect — dependency array correct? cleanup returned?
 ④ ⭐⭐ EVERY NEW package.json LINE — do you know that package? Is it
    real? Check the download count.
 ⑤ ⭐ EVERY try/catch — does the catch actually do something, or does
    it swallow the error into a blank screen?
 ⑥ ⭐⭐ ANYTHING WITH process.env — is a secret reaching the client?

⭐ THEN ASK: "which part of this are you least confident about?"
  ⭐⭐ IT USUALLY TELLS YOU THE TRUTH, AND THAT IS WHERE THE BUG IS.
```

---

# 3 · ⭐⭐ The Codex cross-check — the final gate

```
⭐⭐ WHY A SECOND MODEL: ONE MODEL REVIEWING ITS OWN CODE SHARES THE
   BLIND SPOT THAT PRODUCED THE BUG.
   ⭐ This is not about which model is better. It is that the OVERLAP
     of two models' blind spots is smaller than either alone.

RUN IT WHEN THE DIFF TOUCHES:
   money · authentication / authorization · customer data ·
   file upload · anything that sends email or SMS · a new public endpoint
```

```
⭐⭐ THE PROMPT. NEVER ASK "IS THIS GOOD?"

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
  ⭐⭐ If you find nothing, say so — do not invent findings."

⭐ THE LAST LINE IS ESSENTIAL. Without it you get invented findings and
  cannot separate them from the real ones.
```

| ⭐ Outcome | Meaning |
|---|---|
| Both clean | ⭐ Reasonable confidence. Ship. |
| **They disagree** | ⭐⭐ **The valuable case — go look yourself.** One is wrong, and finding out which teaches you the codebase |
| Both find the same thing | It is real. Fix it. |
| Second finds what the first missed | ⭐ Exactly why you ran it |

---

# 4 · Tests that earn their place

```
⭐⭐ THE FILTER: A TEST IS WORTH HAVING ONLY IF IT WOULD FAIL FOR A
   REASON SOMEONE WOULD CARE ABOUT.

 ✅ WORTH IT                          ❌ THEATRE
 · ⭐ the money path, end to end       · that a component renders
 · ⭐⭐ AUTH: wrong user gets 403       · that a getter returns a field
 · the failure path (500, timeout,     · snapshot tests of whole pages
   offline, empty)                       ⭐⭐ (they fail on every real
 · ⭐ idempotency: same request twice     change, so people run -u and
   = one record                           commit without reading — a
 · a bug you already had once            test that TRAINS you to
                                          approve diffs blindly)
```

```
□ ⭐⭐ MOCK AT THE NETWORK, NOT THE MODULE (MSW).
   ⭐ A module mock means renaming a function leaves the test passing
     while the app is broken — it asserted a call, not a behaviour.
□ ⭐ THE HANDLERS THAT MATTER ARE THE FAILURE ONES:
   a 500 with an HTML body · a 403 · a slow response · offline
   ⭐⭐ MOST SUITES TEST ONLY THE SUCCESS PATH, which already works.
□ Query by ROLE, the way assistive tech does — ⭐ if you cannot select
   it by role, a screen reader user cannot find it. Free a11y testing.
□ ⭐ 5–10 END-TO-END TESTS, NOT 200. A 200-test E2E suite is one people
   re-run until it passes, which is the same as having none.
□ ⭐⭐ NEVER A FIXED SLEEP. Wait for a condition.
```

**The edge cases an agent never tests:** empty list · exactly one item · 10,000 items · a name with
an apostrophe or emoji · a very long string · negative and zero · a double-click · two tabs open ·
session expiring mid-action · going offline mid-request · the back button.

---

# 5 · ⭐⭐ Real device, real network

```
□ ⭐⭐ ON A REAL PHONE, ON MOBILE DATA. Not devtools emulation.
   ⭐ Devtools does not show you: real touch, iOS Safari's actual
     behaviour, real latency, or the address bar eating 100vh.
□ ⭐ TEST SAFARI ON iOS SPECIFICALLY. It is not Chrome.
□ Chrome, Safari, Firefox on desktop
□ ⭐ THROTTLE TO SLOW 4G + 6× CPU and click before it is ready.
   ⭐⭐ ANYTHING THAT BREAKS UNDER SLOW WILL BREAK IN A DEMO, ON HOTEL
     WIFI, IN FRONT OF SOMEONE.
□ ⭐ GO OFFLINE MID-ACTION. Is there a message, or a hang?
□ ⭐⭐ A FRESH ACCOUNT WITH NO DATA. The first-run experience is the one
   you never see, because your account has been full for weeks.
□ Tab the whole app with no mouse
□ 200% browser zoom
```

---

# 6 · CI — the checks that run without you

```
□ typecheck · lint · test on every PR
□ ⭐ BUILD MUST SUCCEED — including production env vars present
□ ⭐⭐ SECRET SCAN ON THE BUILD OUTPUT:
     grep -rEn "sk_live|service_role|BEGIN PRIVATE" .next/ dist/
□ npm audit --omit=dev, fail on high
□ ⭐ BUNDLE SIZE BUDGET — fail when exceeded, so growth is a decision
□ Lighthouse CI on the preview deploy
□ ⭐ A PREVIEW DEPLOY PER PR — reviewers click instead of imagining
□ ⭐⭐ BRANCH PROTECTION: PR required, CI required, no force-push to main
```

---

# 7 · After deploy — the smoke test

```
⭐⭐ RUN THIS IMMEDIATELY AFTER EVERY PRODUCTION DEPLOY. 60 SECONDS.

 □ the site loads on the real domain
 □ ⭐ LOG IN WORKS
 □ the main workflow completes once, end to end
 □ ⭐⭐ HARD-REFRESH A DEEP ROUTE (/orders/123) — must not 404.
    ⭐ The dev server handles it; nginx and S3 do not. You find out
      when someone shares a link.
 □ ⭐ THE CONSOLE IS CLEAN
 □ cookies show Secure + HttpOnly in the Application tab
    ⭐ Secure cookies SILENTLY do not set over plain HTTP
 □ ⭐ TRIGGER A REAL ERROR AND CONFIRM IT ARRIVES IN SENTRY.
    ⭐⭐ INSTALLED IS NOT THE SAME AS WORKING.
 □ curl /.env and /.git/config ⇒ 404
 □ ⭐⭐ ROLLBACK IS ONE COMMAND AND YOU HAVE RUN IT ONCE
```

---

**Back:** [folder index](README.md) · **Workflow:** [01-Workflow.md](01-Workflow.md) ·
**Ship:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
