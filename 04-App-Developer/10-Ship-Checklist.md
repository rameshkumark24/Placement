# 🚦 Ship Checklist — before you submit

> ⭐⭐ **The stakes are higher than web.** A bug you ship is live for one to three days minimum, and
> old versions never die. **This checklist is the last cheap moment.**

**How to use it:** paste one section into the chat and say *"audit against this and fix every
failure."* Section by section — not all at once.

---

# Section 1 — Content

```
□ ⭐⭐ NO PLACEHOLDER TEXT — grep for: "lorem", "Feature One", "TODO",
   "Your Company", "Coming soon", "Example", "John Doe"
□ ⭐ NO PLACEHOLDER IMAGES OR FAKE PEOPLE
□ Real copy, spell-checked, on every screen
□ ⭐ EVERY IMAGE AND ICON HAS AN ACCESSIBILITY LABEL
□ Dates human-readable; numbers and money formatted
□ No text promising something the app cannot do
□ ⭐⭐ THE APP NAME AND ICON ARE FINAL — not the Expo default
```

---

# Section 2 — ⭐⭐ Layout on a real device

```
□ ⭐⭐ NO HORIZONTAL OVERFLOW anywhere, on the SMALLEST device you support
   ⭐ Usual causes: a fixed width · a long unbroken string · an image
     with no resizeMode · a Row that does not wrap · padding pushing
     past the screen

□ ⭐⭐ SAFE AREAS RESPECTED — use SafeAreaView / useSafeAreaInsets
   ⭐ Content under the notch, or a button under the home indicator, is
     the most obvious "nobody tested this" signal there is.

□ ⭐⭐ THE KEYBOARD DOES NOT COVER THE INPUT OR THE SUBMIT BUTTON.
   ⭐ TEST EVERY FORM WITH THE KEYBOARD OPEN, ON A SMALL DEVICE.
     KeyboardAvoidingView behaves DIFFERENTLY on iOS and Android —
     test both, not one.
□ Scroll views actually scroll when the keyboard is up
□ ⭐ A WAY TO DISMISS THE KEYBOARD (tap outside / a Done button)

□ Tap targets ≥ 44pt iOS / 48dp Android, and not adjacent
□ ⭐⭐ TESTED AT THE LARGEST SYSTEM FONT SIZE — text must wrap, not clip.
   ⭐ A meaningful number of people run their phone at max text size.
□ Landscape works, or is locked deliberately
□ ⭐ TESTED ON A SMALL SCREEN (SE-class) AND A LARGE ONE (Pro Max/tablet)
□ Dark mode works, or is disabled deliberately
```

---

# Section 3 — ⭐⭐ Network & offline

```
□ ⭐⭐ AN OFFLINE STATE ON EVERY SCREEN THAT NEEDS THE NETWORK.
   ⭐ NOT AN INFINITE SPINNER. The most common mobile bug there is.

□ ⭐ TEST IN AIRPLANE MODE. Then test on a THROTTLED, LOSSY connection
   — ⭐⭐ WHICH IS WORSE THAN NO CONNECTION, because requests hang
   instead of failing fast.
□ Every request has a timeout
□ ⭐⭐ TEST THE CAPTIVE-PORTAL CASE: connected to wifi, no internet.
   ⭐ NetInfo says "connected". Nothing works. Handle it.
□ Retries capped with backoff + jitter — ⭐⭐ IN A TUNNEL EVERY REQUEST
   FAILS, and an uncapped loop drains the battery and bills you
□ ⭐ QUEUED OFFLINE WRITES ARE IDEMPOTENT — they will send twice
□ The user can see what is unsynced
□ ⭐⭐ KILL THE APP MID-REQUEST AND REOPEN IT. What state is it in?
```

---

# Section 4 — ⭐⭐ States

```
□ ⭐ LOADING — a skeleton shaped like the content, not a bare spinner
□ ⭐⭐ ERROR — what happened + what to do + RETRY. Branch by cause:
     offline · timeout · 401 · 403 · 404 · 429 · 5xx
   ⭐⭐ NEVER CLEAR A FORM ON ERROR.
□ ⭐⭐ EMPTY — AND IT IS TWO STATES:
     · never-had-any   ⇒ explain + THE BUTTON THAT CREATES THE FIRST
     · filtered-to-zero ⇒ "No results for X" + [Clear]
   ⭐ Showing "nothing here yet" to someone with 500 records and a bad
     filter makes them think their data is gone.
□ ⭐⭐ SUCCESS — VISIBLE CONFIRMATION ON EVERY ACTION.
   ⭐ Silence reads as failure and people tap again — which is how you
     get two orders. Better than a toast: the screen actually changes.
□ ⭐ PULL-TO-REFRESH on any list that goes stale
□ A "something went wrong" screen instead of a white screen on crash
```

---

# Section 5 — ⭐⭐ Performance

```
□ ⭐⭐ 60fps ON A LOW-END ANDROID. Not on your iPhone.
   ⭐ Buy or borrow a cheap Android. It is the single most useful
     testing purchase you can make.
□ ⭐ COLD START UNDER 2 SECONDS
□ ⭐⭐ LISTS ARE VIRTUALISED — FlashList or FlatList.
   ⭐ .map() over 500 rows renders 500 components at once and janks.
□ ⭐ IMAGES RESIZED SERVER-SIDE AND CACHED.
   ⭐⭐ NEVER PUT A FULL-RESOLUTION PHOTO IN A LIST — it is the #1
     cause of jank and memory crashes on Android.
□ Animations on the native driver (useNativeDriver: true)
□ ⭐ NO WORK IN render(). No API call in render, ever.
□ ⭐⭐ APP SIZE CHECKED — every MB costs installs on slow connections
   and in markets with expensive data
□ Memory profiled on a long session — ⭐ navigate 50 screens and watch
□ Battery: no polling, no background timers, no wake locks you forgot
```

---

# Section 6 — ⭐⭐ Security

```
□ ⭐⭐ THE ID-SWAP TEST: log in as A, request B's id via the API.
   MUST be 403/404. ⭐ This is the #1 real data leak.
□ ⭐⭐ TOKENS IN expo-secure-store — NOT AsyncStorage.
   ⭐ AsyncStorage is plain text on disk.
□ ⭐⭐ UNZIP YOUR OWN BUILD AND GREP IT:
     unzip -p app.apk | strings | grep -iE "sk_live|secret|api[_-]?key"
   ⭐ Anything there is public. Obfuscation is not encryption.
□ ⭐ NO SECRET FOR A PAID SERVICE IN THE APP. The app calls YOUR API;
   YOUR API holds the key, the rate limit and the spend cap.
□ Permissions requested at point of use, with a reason string that
   explains WHY — ⭐ and the app still works if denied
□ ⭐ CERTIFICATE PINNING if the data is sensitive
□ Uploads validated by content, size-capped, ⭐⭐ EXIF STRIPPED
□ ⭐ LOGOUT CLEARS EVERYTHING LOCAL — secure store, cache, query cache,
   files. ⭐⭐ Otherwise the next person to use the phone sees it.
□ No PII in logs or crash reports
□ Deep links validated — ⭐ a deep link is untrusted input from anyone
```

---

# Section 7 — ⭐⭐ Store submission

```
□ ⭐⭐ THE PAYMENT TYPE IS CORRECT — AND THIS IS THE #1 REJECTION:
     DIGITAL goods/subscriptions ⇒ ⭐ MUST use Apple/Google IAP
     PHYSICAL goods/services     ⇒ ⭐ MUST NOT use IAP — Stripe etc.
   ⭐⭐ GETTING THIS BACKWARDS IS AN AUTOMATIC REJECTION, AND IT COSTS
     YOU A REVIEW CYCLE EACH TIME.

□ ⭐ PRIVACY POLICY URL IS LIVE AND REACHABLE. Rejected if it 404s.
□ ⭐⭐ THE DATA-SAFETY / PRIVACY-NUTRITION FORM MATCHES REALITY —
   INCLUDING WHAT YOUR SDKs COLLECT. ⭐ Analytics and crash reporting
   collect more than you think, and a mismatch is a rejection or a
   later removal.
□ ⭐ A DEMO ACCOUNT FOR REVIEW, and you have tested that it works.
   ⭐⭐ A login wall with no working demo account is an instant reject.
□ Screenshots at every required size, real content, no placeholder art
□ App icon at every size, no alpha channel on iOS
□ Description, keywords, category, age rating, export compliance
□ ⭐ PERMISSION USAGE STRINGS EXPLAIN WHY, in plain language.
   "This app needs camera access" is rejected. "To scan the barcode on
   your receipt" is not.
□ ⭐ NO MENTION OF OTHER PLATFORMS in the listing
□ Test the ACTUAL BUILD you are submitting — ⭐⭐ TestFlight / internal
   track, on a real device, not the dev build
```

---

# Section 8 — ⭐⭐ Release & recovery

```
□ ⭐ SENTRY WITH NATIVE SYMBOLICATION — and you have triggered a real
   crash and seen a readable stack trace.
   ⭐⭐ A CRASH REPORT WITHOUT SYMBOLS IS USELESS, and you only find
     out during the incident.
□ ⭐ CRASH-FREE RATE VISIBLE. Target > 99.5%.
□ ⭐⭐ THE OTA UPDATE PATH TESTED END TO END, ONCE, BEFORE YOU NEED IT.
   ⭐ Know what OTA can and cannot fix: JS yes, native no.
□ ⭐⭐ A STAGED ROLLOUT — 5% → 20% → 50% → 100%, watching crash rate.
   ⭐ NEVER 100% ON DAY ONE. This is the cheapest insurance you have.
□ ⭐⭐ A FORCE-UPDATE MECHANISM EXISTS — a version check against a
   server value, so you can block a broken build.
   ⭐ You cannot recall a release. This is the only kill switch.
□ Version and build number incremented correctly
□ ⭐ TESTED ON: oldest supported iOS · oldest supported Android ·
   a cheap Android · a small screen · a large screen
□ ⭐ THE PREVIOUS VERSION STILL WORKS AGAINST YOUR API.
   ⭐⭐ OLD VERSIONS NEVER DIE. Confirm you did not break them.
```

---

# The 90-second version

```
① ⭐⭐ ID-swap test — can user A read user B's data?
② ⭐⭐ Airplane mode — is there an offline state, or a spinner?
③ ⭐ Open every form with the keyboard up — is the button reachable?
④ ⭐⭐ Run it on a cheap Android — does it stutter?
⑤ ⭐ Filter a list to zero — which empty state?
⑥ ⭐⭐ Unzip the build and grep for secrets
⑦ ⭐ Max system font size — does text clip?
⑧ ⭐⭐ Kill the app mid-action and reopen — what state?
⑨ ⭐ Digital or physical goods — is the payment type right?
⑩ ⭐⭐ Deny every permission — does it still work?
```

---

**Back:** [folder index](README.md) · **The memory:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md)
