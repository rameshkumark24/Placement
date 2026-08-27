# 🚀 Release & After — you cannot recall a release

> ⭐⭐ **This is the difference that matters most.** On the web you roll back in seconds. Here the
> broken version is live for one to three days minimum, and **users who already installed it keep
> it forever unless you can make them update.**

---

# 1 · ⭐⭐ Before you submit

```
□ ⭐⭐ THE FULL SHIP CHECKLIST PASSED — [10-Ship-Checklist.md](10-Ship-Checklist.md)
□ ⭐ TESTED ON THE ACTUAL BUILD YOU ARE SUBMITTING, via TestFlight or
   the internal track — ⭐⭐ NOT THE DEV BUILD. They differ.
□ ⭐ THE PREVIOUS VERSION STILL WORKS AGAINST YOUR API.
   ⭐⭐ OLD VERSIONS NEVER DIE. Confirm you did not break them.
□ ⭐ A DEMO ACCOUNT FOR REVIEW, AND YOU LOGGED IN WITH IT YOURSELF
□ Privacy policy URL live — ⭐ a 404 is a rejection
□ ⭐ THE DATA-SAFETY FORM MATCHES REALITY, including SDK collection
□ ⭐⭐ THE PAYMENT TYPE IS CORRECT (IAP vs Stripe)
□ Version and build number incremented
□ ⭐ THE FORCE-UPDATE SWITCH EXISTS AND YOU HAVE TESTED IT
□ ⭐⭐ THE OTA PATH TESTED ONCE, END TO END
```

---

# 2 · ⭐⭐ Staged rollout — the cheapest insurance you have

```
⭐⭐ NEVER 100% ON DAY ONE. NOT ONCE. NOT FOR A SMALL CHANGE.

  5%  ⇒ ⭐ WATCH THE CRASH-FREE RATE FOR 24 HOURS
  20% ⇒ watch another 24
  50% ⇒ watch
  100%

⭐ ANDROID: staged rollout is built in, and you can HALT it.
⭐ iOS: phased release over 7 days — ⭐⭐ AND YOU CAN PAUSE IT.
  Know where that button is BEFORE you need it.

⇒ ⭐⭐ A CRASH AT 5% AFFECTS 5% OF USERS. THE SAME CRASH AT 100%
  AFFECTS EVERYONE AND SITS IN YOUR REVIEWS PERMANENTLY.
```

---

# 3 · ⭐⭐ When something is broken

```
⭐⭐ THE ORDER. THE FIRST STEP IS NOT "FIX IT".

 ① ⭐⭐ HALT THE ROLLOUT. Immediately. Before diagnosing anything.
    ⭐ This is the one action that limits the damage, and it takes
      thirty seconds.
 ② ⭐ IS IT JAVASCRIPT OR NATIVE?
    JS    ⇒ ⭐⭐ OTA UPDATE. Minutes.
    NATIVE ⇒ ⭐ a new build and a review. 1–3 days.
             ⇒ ⭐⭐ USE THE FORCE-UPDATE SWITCH if it is severe:
               block the broken version, tell users to update.
 ③ ⭐ IF IT IS SERVER-SIDE, FIX IT SERVER-SIDE. Often the fastest
    real fix — a feature flag off, an endpoint corrected.
    ⭐⭐ THIS IS WHY SERVER-SIDE FEATURE FLAGS ARE WORTH HAVING ON
      MOBILE: they are a kill switch that does not need a review.
 ④ Capture evidence before it rotates.
 ⑤ Fix forward, with a test that would have caught it.
 ⑥ ⭐ WRITE IT DOWN: what happened · what it affected · the cause ·
    what you changed · ⭐⭐ WHAT WOULD HAVE CAUGHT IT EARLIER.
```

> ⭐ **Blame the missing guardrail, not the mistake.** "I forgot" is not a cause. "Nothing tested it
> on Android 10" is a cause, and it has a fix.

---

# 4 · ⭐⭐ Feature flags are more valuable here than on the web

```
⭐⭐ A SERVER-CONTROLLED FLAG IS A KILL SWITCH THAT NEEDS NO REVIEW.

  □ ⭐ SHIP RISKY FEATURES BEHIND A FLAG, DEFAULT OFF
  □ ⭐⭐ TURN IT OFF FROM THE SERVER IF IT MISBEHAVES — no OTA, no
     build, no review, no waiting
  □ ⭐ THE APP MUST BEHAVE SENSIBLY IF THE FLAG SERVICE IS
     UNREACHABLE — cache the last known values, and pick a safe default
  □ Remove flags once a feature is stable — ⭐ dead flags accumulate
     and become a second, undocumented configuration system
```

---

# 5 · The first 48 hours

```
□ ⭐⭐ WATCH THE CRASH-FREE RATE. Target > 99.5%.
   ⭐ Below 99% is an emergency; halt the rollout.
□ ⭐ LOOK FOR CRASHES CLUSTERED ON ONE OS VERSION OR DEVICE — that
   pattern tells you exactly what to fix
□ ⭐⭐ READ EVERY REVIEW. Reviews are your only feedback channel from
   users who will never contact you — and they are permanent, public,
   and they affect your ranking.
   ⭐ REPLY TO THEM. A reply to a one-star review sometimes turns it.
□ ⭐ WATCH THE FUNNEL — installs vs completed signups vs the main
   action. People who give up silently are the majority.
□ Watch the bill
□ ⭐ DO NOT SHIP FEATURES. Fix what the release revealed.
```

---

# 6 · Ongoing

| ⭐ When | Do |
|---|---|
| **Daily (first week)** | Crash-free rate · reviews · the funnel · the bill |
| **Weekly** | Dependency PRs · error trends · ⭐ new OS beta warnings |
| **Monthly** | ⭐⭐ Restore a backup · review spend caps · check store policy changes |
| **Per OS release** | ⭐⭐ **Test on the new iOS/Android beta BEFORE it ships.** ⭐ A new OS version breaking your app is a real and recurring event, and you get warning if you look. |
| **Quarterly** | Re-run [05-Security.md](05-Security.md) — especially the ID-swap test · re-run the [ship checklist](10-Ship-Checklist.md) |

---

# 7 · ⭐⭐ Keeping old versions working

```
⭐⭐ USERS DO NOT UPDATE. YOUR API IS SERVING A BUILD FROM EIGHT
   MONTHS AGO, TODAY.

□ ⭐ EVERY API CHANGE IS ADDITIVE. Add fields; never remove or rename.
□ ⭐⭐ TO REMOVE SOMETHING: add the new field · ship the app version
   that uses it · WAIT for old versions to fall below a threshold ·
   then remove the old field.
   ⭐ "Wait" needs a NUMBER. Send the app version with every request
     and log it — then "how many users are on the old contract?" is
     a query, not a guess.
□ ⭐ NEW ENUM VALUES BREAK OLD CLIENTS that switch exhaustively.
   ⭐⭐ Ship a default/unknown branch BEFORE you add the value.
□ ⭐ SET A MINIMUM SUPPORTED VERSION and enforce it with the
   force-update switch — ⭐⭐ but give people warning, and make sure
   the update actually exists in the store first
□ Decide how far back you support, and write it down
```

---

# 8 · The tech debt log

```
⭐⭐ EVERY TIME YOU SHIP SOMETHING YOU KNOW IS NOT RIGHT:

   <what> · <why it is wrong> · <what it costs later> · REVISIT WHEN

 ⭐ Example:
   "No pagination on the activity list · loads everything · will jank
    on a cheap Android past ~2,000 rows · REVISIT WHEN a user passes
    500 items"

⇒ ⭐⭐ THE "REVISIT WHEN" IS THE WHOLE VALUE. It turns a shortcut into
  a decision with a trigger.
```

---

**Back:** [folder index](README.md) · **Ship:** [10-Ship-Checklist.md](10-Ship-Checklist.md) ·
**Distribution options:** [Reference/Distribution-Options.md](Reference/Distribution-Options.md)
