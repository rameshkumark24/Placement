# 🤖 AI Features — on a device you don't control

> ⭐⭐ **Everything in the [web version](../03-Web-Developer/13-AI-Features.md) applies.** This file is
> what mobile adds: **the key cannot live in the app, the network will drop mid-stream, the store has
> its own AI rules, and you cannot hotfix a prompt.**

⭐ **If your app has no AI feature, skip this file.**

---

# 1 · ⭐⭐ The key cannot be in the app

```
⭐⭐ THIS IS WORSE THAN ON WEB, NOT BETTER.
   ⭐ On web a leaked key is in a bundle. In an app it is in a file
     anyone can download from the store, unzip, and grep — ⭐⭐ AND
     UNLIKE A WEB BUNDLE YOU CANNOT ROTATE IT AWAY WITH ONE DEPLOY,
     BECAUSE OLD APP VERSIONS KEEP USING IT.

  ❌ NEVER: a provider API key in the app, in any form, obfuscated
     or not. ⭐ Obfuscation is not encryption.
  ✅ ⭐⭐ THE APP CALLS **YOUR** API. YOUR API HOLDS THE KEY, THE
     QUOTA, THE RATE LIMIT AND THE SPEND CAP.

□ ⭐ AUTH REQUIRED on the AI endpoint. ⭐⭐ AN OPEN AI ENDPOINT IS
   SOMEONE ELSE'S FREE API BILLED TO YOU — and an APK makes your
   endpoints trivially discoverable.
□ ⭐ PER-USER QUOTA — daily and monthly, not just per-minute
□ ⭐⭐ A HARD SPEND CAP AT THE PROVIDER, WITH AN ALERT BELOW IT
```

---

# 2 · ⭐⭐ The network will drop mid-response

```
⭐⭐ AI RESPONSES TAKE SECONDS. ON MOBILE, SECONDS IS LONG ENOUGH FOR
   THE USER TO WALK INTO A LIFT.

□ ⭐ STREAM IT. A response that appears word by word is tolerable at
   ten seconds; a spinner is not.
□ ⭐⭐ HANDLE A BROKEN STREAM: partial output, then a clear "the
   connection dropped" with a RETRY — ⭐ never a half-message the
   user thinks is the whole answer.
□ ⭐⭐ IF THE APP IS BACKGROUNDED MID-REQUEST, THE OS MAY KILL IT.
   ⭐ Decide: does the request continue server-side and get collected
     later, or is it lost? Tell the user which.
□ ⭐ NEVER AUTO-RETRY A PAID GENERATION ON RECONNECT WITHOUT ASKING —
   ⭐⭐ YOU PAY FOR EVERY ATTEMPT, and in a tunnel that is a loop.
□ ⭐ TIMEOUT GENEROUSLY (AI is slow) BUT NOT INFINITELY
□ ⭐ SAVE THE USER'S INPUT LOCALLY BEFORE SENDING. Losing a long
   prompt to a dropped connection is a user lost.
```

---

# 3 · ⭐⭐ You cannot hotfix a prompt

```
⭐⭐ IF THE PROMPT IS COMPILED INTO THE APP, FIXING IT NEEDS A STORE
   REVIEW. 1–3 DAYS, WITH THE BAD BEHAVIOUR LIVE THE WHOLE TIME.

  ⇒ ⭐⭐ **SERVE THE PROMPT FROM YOUR SERVER, NOT FROM THE BUNDLE.**
    ⭐ Then a bad prompt is a config change in minutes, and you can
      A/B it, version it, and roll it back.
  ⇒ ⭐ THE SAME ARGUMENT AS A SERVER-SIDE FEATURE FLAG: it is a kill
    switch that needs no review.

□ ⭐ THE MODEL CHOICE IS ALSO SERVER-SIDE — so you can switch provider
   or downgrade to a cheaper model without shipping a build
□ ⭐⭐ THE WHOLE AI FEATURE SITS BEHIND A SERVER FLAG YOU CAN TURN OFF.
   ⭐ If it misbehaves publicly, you disable it in seconds instead of
     waiting for review.
□ ⭐ THE APP MUST BEHAVE SENSIBLY WHEN THE FEATURE IS OFF
```

---

# 4 · ⭐⭐ Prompt injection and untrusted output

Same rules as web — the mobile difference is that **the app itself is a weaker boundary.**

```
□ ⭐⭐ THE MODEL GETS NO CAPABILITY THE USER DOES NOT HAVE, enforced
   SERVER-SIDE. ⭐ Never enforce a tool permission in the app — the
   app is not a boundary.
□ ⭐⭐ THE DANGEROUS COMBINATION: untrusted content + private data +
   a way to send data out. Break any one.
   ⭐ "A way out" includes rendering a remote image — an image URL in
     AI output can exfiltrate on render. Block external images.
□ ⭐ IRREVERSIBLE ACTIONS NEED AN EXPLICIT TAP. The model proposes,
   the user approves.
□ ⭐⭐ OUTPUT NEVER GOES INTO: a WebView · a deep link it constructs ·
   a file path · a shell · innerHTML.
   ⭐ A WebView rendering model output is a browser you shipped,
     showing content an attacker may control.
□ ⭐ VALIDATE STRUCTURED OUTPUT before use. Schema-valid ≠ true.
□ ⭐ NEVER PUT A SECRET IN THE PROMPT. Assume it is extractable.
```

---

# 5 · Cost, battery and data

```
□ ⭐ CAP INPUT AND OUTPUT LENGTH — ⭐⭐ the p99 request is usually a
   bug, not a heavy user
□ ⭐⭐ CONVERSATION HISTORY GROWS EVERY TURN AND YOU PAY FOR ALL OF IT
   EVERY TIME. Window it or summarise it.
□ ⭐ CACHE IDENTICAL REQUESTS — key on input + model + prompt version
   (⭐⭐ AND THE USER, if the content is personal)
□ ⭐⭐ ON-DEVICE MODELS ARE AN OPTION NOW, AND THE TRADE IS REAL:
     ✅ ⭐ no per-request cost · works offline · nothing leaves the
        device (a genuine privacy story)
     ❌ ⭐ app size · battery and heat · much weaker output · device
        support you must gate on
   ⇒ ⭐⭐ GOOD FOR: classification, small extraction, on-device search.
     ⭐ NOT for open-ended generation on a mid-range phone.
□ ⭐ A LONG GENERATION HEATS THE PHONE AND DRAINS BATTERY — users see
   this in Settings. ⭐⭐ Do not run generation in a loop or in the
   background.
□ ⭐ WARN BEFORE LARGE MODEL DOWNLOADS ON CELLULAR
□ ⭐ KNOW YOUR COST PER USER PER MONTH
```

---

# 6 · ⭐⭐ The store rules for AI

```
⭐⭐ BOTH STORES HAVE SPECIFIC AI EXPECTATIONS, AND THEY ARE ENFORCED
   AT REVIEW.

□ ⭐⭐ IF USERS CAN GENERATE CONTENT WITH AI, YOU HAVE USER-GENERATED
   CONTENT — SO APPLE'S FOUR REQUIREMENTS APPLY: filtering, reporting,
   blocking, and contact info.
   ⇒ ⭐ THIS SURPRISES PEOPLE. A free-text AI box counts.
   (→ [12-Legal-and-Compliance.md §4](12-Legal-and-Compliance.md))
□ ⭐⭐ MODERATE INPUT **AND** OUTPUT. A public app with an unfiltered
   generation box will be used to produce something you do not want
   your app's name on — and the review team tests exactly that.
□ ⭐ THE AGE RATING MUST REFLECT THAT OUTPUT IS NOT FULLY PREDICTABLE
□ ⭐ DECLARE THE AI PROVIDER IN YOUR PRIVACY FORM — ⭐⭐ YOU ARE
   SENDING USER CONTENT TO A THIRD PARTY, and it must be disclosed
□ ⭐ LABEL AI OUTPUT AS AI-GENERATED. Expected by reviewers, and
   ⭐⭐ BECOMING A LEGAL REQUIREMENT IN SOME MARKETS.
□ ⭐ DO NOT IMPLY A PARTNERSHIP with a model provider you do not have,
   and do not use their name or logo in your listing
```

---

# 7 · Hallucination on a small screen

```
□ ⭐⭐ NEVER PRESENT MODEL OUTPUT AS FACT WITHOUT A SOURCE.
   ⭐ On mobile there is less room to show caveats, so the design must
     carry it — a source line, a label, a tap-to-verify.
□ ⭐ EXPLICITLY ALLOW "I don't know" IN THE PROMPT. ⭐⭐ Abstention is
   the most valuable line in a prompt and almost nobody includes it.
□ ⭐⭐ IDs, PRICES, DATES AND POLICIES COME FROM YOUR DATABASE, NEVER
   FROM THE MODEL. A made-up refund policy quoted to a customer is a
   real liability.
□ ⭐ A HUMAN PATH OUT on anything that matters
□ ⭐ AN EASY WAY TO REPORT A BAD RESPONSE — ⭐⭐ AND IT IS ALSO YOUR
   BEST EVAL DATA
```

---

# 8 · ⭐⭐ Before you ship an AI feature

```
□ ⭐⭐ NO PROVIDER KEY IN THE APP — the call goes through your API
□ ⭐ AUTH + PER-USER QUOTA + RATE LIMIT + HARD SPEND CAP
□ ⭐⭐ THE PROMPT AND THE MODEL CHOICE ARE SERVED FROM YOUR SERVER
□ ⭐⭐ THE WHOLE FEATURE IS BEHIND A SERVER FLAG YOU CAN TURN OFF
□ ⭐ INPUT AND OUTPUT LENGTH CAPPED; history windowed
□ ⭐⭐ STREAMING, WITH A BROKEN-STREAM STATE AND A RETRY
□ ⭐ THE USER'S INPUT IS SAVED LOCALLY BEFORE SENDING
□ ⭐⭐ NO TOOL THE USER COULD NOT DO THEMSELVES; enforced server-side
□ ⭐ OUTPUT SANITISED; never into a WebView or a constructed deep link
□ ⭐⭐ INPUT **AND** OUTPUT MODERATED
□ ⭐⭐ UGC REQUIREMENTS MET: report, block, filter, contact
□ ⭐ AI OUTPUT LABELLED; sources shown; "I don't know" allowed
□ ⭐ THE PROVIDER DECLARED IN THE PRIVACY FORM AND POLICY
□ ⭐⭐ PROMPT LOGS SCRUBBED, RETENTION SET, ACCESS CONTROLLED
□ ⭐ AN EVAL SET — 20 real inputs, run before every prompt change
□ ⭐ THE APP WORKS WITH THE FEATURE OFF
□ ⭐⭐ YOU CAN STATE YOUR COST PER USER PER MONTH
```

---

> ⭐ **The full version:** [`01-Python-Developer` Days 414–461](../01-Python-Developer/Days/Day-414.md)
> — 48 days on retrieval, agents, evals, safety and production.

---

**Back:** [folder index](README.md) · **Web version:**
[`03-Web-Developer/13-AI-Features.md`](../03-Web-Developer/13-AI-Features.md) ·
**Legal:** [12-Legal-and-Compliance.md](12-Legal-and-Compliance.md)
