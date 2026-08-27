# 🧪 Testing & Review — including the Codex final check

> ⭐⭐ **On mobile, "it works" means "it works on a device, on a real network, on both platforms."**
> Everything else is a guess — and you cannot hotfix a wrong guess.

---

# 1 · The review order

```
 ① ⭐ THE SIX-POINT SKIM (§2)      — 2 minutes, by eye
 ② /code-review
 ③ /security-review               — auth, data, payments, storage,
                                     permissions
 ④ ⭐⭐ CODEX CROSS-CHECK (§3)
 ⑤ ⭐⭐ THE DEVICE MATRIX (§5)      — the one that actually matters
 ⑥ THE TESTS THAT EARN THEIR PLACE (§4)
 ⑦ ⭐⭐ THE SHIP CHECKLIST          — [10-Ship-Checklist.md](10-Ship-Checklist.md)
```

---

# 2 · The six-point skim

```
 ① ⭐ THE DIFF STAT — more files than expected? Ask why.
 ② ⭐⭐ EVERY QUERY — does it filter by the current user?
 ③ ⭐ EVERY useEffect — primitive deps? cleanup returned?
    ⭐⭐ EVERY LISTENER — is it removed?
 ④ ⭐⭐ EVERY NEW DEPENDENCY — is it real, and DOES IT ADD NATIVE CODE?
 ⑤ ⭐ EVERY try/catch — does the catch do something, or blank the screen?
 ⑥ ⭐⭐ ANY WRITE TO STORAGE — secure store or AsyncStorage? Is that right?

⭐ THEN ASK: "which part are you least confident about?"
```

---

# 3 · ⭐⭐ The Codex cross-check

```
⭐⭐ ONE MODEL REVIEWING ITS OWN CODE SHARES THE BLIND SPOT THAT MADE
   THE BUG. Run a second model on: money · auth · customer data ·
   uploads · ⭐ ANYTHING WRITING TO LOCAL STORAGE · permissions.

THE PROMPT — never "is this good?":

 "Here is a diff. Find bugs. Check specifically:
  ① Can a logged-in user reach another user's data by changing an ID?
  ② Can this loop, retry, or fire more than once? What does it cost
     in requests AND in battery?
  ③ ⭐⭐ What happens if the network drops HALFWAY THROUGH this?
  ④ ⭐⭐ What happens if the OS kills the app right here?
  ⑤ What input breaks it — empty, null, huge, wrong type, unicode?
  ⑥ Is anything secret or personal written to storage or logs?
  ⑦ Does this behave differently on Android than on iOS?
  List concrete failures with line numbers. Do not summarise the code.
  ⭐ If you find nothing, say so — do not invent findings."
```

| Outcome | Meaning |
|---|---|
| Both clean | ⭐ Reasonable confidence |
| **They disagree** | ⭐⭐ **Go look yourself — the valuable case** |
| Both find the same | It is real |
| Second finds what the first missed | ⭐ Exactly why you ran it |

---

# 4 · Tests that earn their place

```
⭐⭐ THE FILTER: WOULD IT FAIL FOR A REASON SOMEONE WOULD CARE ABOUT?

 ✅ WORTH IT                         ❌ THEATRE
 · ⭐ the money path end to end       · that a component renders
 · ⭐⭐ AUTH: wrong user gets 403      · snapshot tests of screens
 · ⭐ OFFLINE behaviour                 ⭐⭐ (they fail on every real
 · the failure path (timeout,           change, so people update and
   5xx, empty)                          commit blind)
 · ⭐ IDEMPOTENCY: same request        · testing a library
   twice = one record
 · a bug you already had once
```

```
□ ⭐ MOCK AT THE NETWORK (MSW), not the module. A module mock leaves
   the test passing while the app is broken.
□ ⭐⭐ THE HANDLERS THAT MATTER ARE THE FAILURE ONES:
   offline · timeout · 500 · 403 · a slow response
□ ⭐ A FEW E2E FLOWS ONLY (Maestro / Detox) — signup, the main
   workflow, payment. ⭐⭐ NOT 200 TESTS.
□ Never a fixed sleep — wait for a condition
```

**Edge cases an agent never tests:** empty list · exactly one item · 5,000 items · a name with an
emoji · the largest system font · **the keyboard open** · **airplane mode** · **the app killed
mid-action** · a denied permission · a very old OS version · a cold start from a deep link.

---

# 5 · ⭐⭐ The device matrix — the part that actually matters

```
⭐⭐ THE SIMULATOR IS NOT EVIDENCE. It has your Mac's CPU, your wifi,
   no real GPU, no real memory pressure, and no Android at all.

 THE MINIMUM SET:
  □ ⭐ ONE iPHONE
  □ ⭐⭐ ONE CHEAP ANDROID — the single most useful testing purchase
     you can make. Everything janks there first.
  □ ⭐ A SMALL SCREEN (SE class) — where the keyboard covers things
  □ A large screen / tablet
  □ ⭐⭐ THE OLDEST OS VERSION YOU CLAIM TO SUPPORT

 ⭐ EXPO GO IS NOT YOUR APP. Build a dev client early.
```

```
⭐⭐ THE CONDITIONS, NOT JUST THE DEVICES:
  □ ⭐ MOBILE DATA, not wifi
  □ ⭐⭐ AIRPLANE MODE — is there an offline state?
  □ ⭐⭐ A THROTTLED, LOSSY CONNECTION — worse than offline, because
     requests HANG instead of failing fast
  □ ⭐ CAPTIVE-PORTAL WIFI — connected, no internet
  □ Low power mode
  □ ⭐ NEARLY FULL STORAGE
  □ ⭐⭐ LARGEST SYSTEM FONT SIZE
  □ Dark mode
  □ ⭐ EVERY PERMISSION DENIED
  □ ⭐⭐ THE APP FORCE-KILLED MID-ACTION, THEN REOPENED
```

---

# 6 · CI

```
□ typecheck · lint · test on every PR
□ ⭐ THE BUILD MUST SUCCEED ON EAS, not just locally
□ ⭐⭐ SECRET SCAN THE BUILD ARTEFACT:
     unzip -p app.apk | strings | grep -iE "sk_live|secret|api[_-]?key"
□ Audit dependencies, fail on high
□ ⭐ A PREVIEW BUILD PER PR (EAS internal distribution) — reviewers
   install it instead of imagining it
□ Branch protection: PR required, CI required, no force-push
```

---

# 7 · ⭐⭐ After release — the first 48 hours

```
□ ⭐⭐ WATCH THE CRASH-FREE RATE. Target > 99.5%.
   ⭐ A staged rollout means you see this at 5% before it is everyone.
□ ⭐ WATCH FOR CRASHES ON A SPECIFIC OS OR DEVICE — that is the
   pattern that tells you what to fix
□ ⭐ READ EVERY REVIEW in the first week. ⭐⭐ Reviews are your only
   feedback channel from users who will never contact you — and they
   are permanent and public.
□ Watch the funnel — ⭐ people who install and never complete signup
   told you something, silently
□ ⭐⭐ IF SOMETHING IS BROKEN: OTA if it is JavaScript. If it is
   native, halt the rollout FIRST, then fix.
□ ⭐ THE FORCE-UPDATE SWITCH IS YOUR ONLY KILL SWITCH. Know it works.
```

---

**Back:** [folder index](README.md) · **Workflow:** [01-Workflow.md](01-Workflow.md) ·
**Ship:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
