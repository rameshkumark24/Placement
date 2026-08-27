# 📱 App — Agent Context

> **This file is the memory.** Paste it into a new chat (or keep it in Notion and link it) before
> asking for any mobile work. It is written **to the agent**, not to me. It is self-sufficient.

---

# 0 · ⭐⭐ Five facts that change every decision

```
⭐⭐ MOBILE IS NOT WEB WITH A SMALLER SCREEN. THESE FIVE DRIVE EVERYTHING:

 ① ⭐⭐ YOU CANNOT HOTFIX.
    A web bug is fixed in 3 minutes. An App Store review is 1–3 days.
    ⇒ ⭐ A BUG THAT SHIPS IS LIVE FOR A WEEK. So the bar before
      release is genuinely higher, and OTA (Expo Updates) is the only
      fast path — and it cannot change native code.

 ② ⭐⭐ OLD VERSIONS NEVER DIE.
    Users do not update. Your API must serve a build from eight months
    ago. ⇒ ⭐ EVERY API CHANGE IS ADDITIVE OR A MIGRATION. Never a
    breaking change.

 ③ ⭐⭐ THE NETWORK IS HOSTILE.
    Tunnels, lifts, 2G, airplane mode, a captive-portal wifi that
    resolves DNS but blocks everything.
    ⇒ ⭐ "OFFLINE" IS A STATE YOU DESIGN, NOT AN ERROR YOU CATCH.

 ④ ⭐⭐ THE DEVICE IS THE ENEMY.
    The OS kills your app in the background without warning. Battery,
    memory and data are the user's, and they can see what you spend.

 ⑤ ⭐⭐ THE BUNDLE IS PUBLIC.
    Anyone can unzip an APK. There are NO SECRETS in a mobile app —
    obfuscation is not encryption.
```

---

# 1 · How I work

```
I VIBE CODE. You write most of the code.
  ⭐ Keep diffs small and TELL ME WHAT TO LOOK AT.
  ⭐ Never touch files outside the task.
  ⭐⭐ THE FAILURES THAT HURT ME ARE SILENT — a crash only on Android 10,
    a leak only on a slow network, a bill from a retry loop in a tunnel.
```

1. **Plan before code on anything non-trivial** — more than one file, or any auth / payment / data /
   native change. Wait for approval.
2. **Never run `git push`, `git reset --hard`, `git rebase`, a migration, or a store submission.**
3. **Never add a dependency without naming it and why** — ⭐⭐ and in mobile, **check whether it
   requires native code**, because that ends OTA updates for that change.
4. **Never refactor what I did not ask about. Never delete a test to make something pass.**
5. **If you are unsure, say so.**

---

# 2 · The loop

```
 ① PLAN     ⭐ complex ⇒ plan mode, no code, I approve first
 ② COMMIT   checkpoint before you write anything
 ③ BUILD    one feature, small diff
 ④ EXPLAIN  what changed, what to look at, what worried you
 ⑤ REVIEW   /code-review
 ⑥ SECURE   /security-review — auth, data, payments, storage, permissions
 ⑦ ⭐⭐ CROSS CODEX SECOND OPINION — money, auth, customer data. §3.
 ⑧ ⭐⭐ DEVICE  RUN IT ON A REAL PHONE. Simulator is not evidence.
 ⑨ SHIP     only after §10's checklist
```

**Plan first when:** auth / payments / user data · more than ~3 files · a schema change · ⭐⭐ **anything
touching native code, permissions, or the build config** · a vague request ("make it faster", "fix
the design").

---

# 3 · ⭐⭐ The Codex cross-check

```
⭐⭐ ONE MODEL REVIEWING ITS OWN CODE SHARES THE BLIND SPOT THAT MADE
   THE BUG. Run a second model on: money · auth · customer data ·
   file upload · anything that sends email or SMS · a new endpoint ·
   ⭐ ANYTHING THAT WRITES TO LOCAL STORAGE.

THE PROMPT — never "is this good?":

 "Here is a diff. Find bugs. Check specifically:
  ① Can a logged-in user reach another user's data by changing an ID?
  ② Can this loop, retry, or fire more than once? What does it cost?
  ③ ⭐ What happens if the network drops HALFWAY THROUGH this?
  ④ ⭐⭐ What happens if the OS kills the app right here?
  ⑤ What input breaks it — empty, null, huge, wrong type, unicode?
  ⑥ Is anything secret being written to storage or logs?
  List concrete failures with line numbers. Do not summarise the code.
  If you find nothing, say so — do not invent findings."
```

---

# 4 · Stack

| Layer | Default | Rule that survives a swap |
|---|---|---|
| Framework | Expo (React Native) + TypeScript | ⭐ Expo Go is not a real test — build a dev client |
| Navigation | Expo Router / React Navigation | Deep links work from cold start |
| UI | React Native primitives + a small owned component set | ⭐⭐ Platform conventions differ — do not force iOS onto Android |
| Server state | TanStack Query + persistence | ⭐ Offline is a cache decision, not an error handler |
| Storage | `expo-secure-store` for tokens, MMKV/AsyncStorage for the rest | ⭐⭐ **AsyncStorage is NOT encrypted** |
| Backend | Supabase / your API | Authorization server-side, always |
| Payments | ⭐⭐ **Store IAP for digital goods, Stripe for physical** | Getting this wrong is a rejection |
| Errors | Sentry + native symbolication | A stack trace without symbols is useless |
| Builds | EAS Build + EAS Update (OTA) | ⭐ OTA cannot change native code |

**Money as integers in the smallest unit. Dates stored UTC, formatted at render.**

---

# 5 · ⭐⭐ Non-negotiables — customer data

```
 ① ⭐⭐ TOKENS IN expo-secure-store (Keychain / Keystore). NEVER in
    AsyncStorage — ⭐ IT IS PLAIN TEXT ON DISK and readable on a rooted
    or jailbroken device, and in some backups.
 ② ⭐⭐ THERE ARE NO SECRETS IN THE BUNDLE. Anyone can unzip the APK.
    ⭐ No API keys for paid services, no service_role, no signing keys.
    If a call needs a secret, the APP CALLS MY API and MY API holds it.
 ③ ⭐⭐ EVERY QUERY FILTERS BY THE AUTHENTICATED USER, SERVER-SIDE.
    ⇒ THE ID-SWAP TEST: log in as A, request B's id. Must be 403/404.
 ④ RLS on and forced.
 ⑤ ⭐ NEVER LOG PII — and remember crash reports leave the device.
 ⑥ ⭐⭐ CERTIFICATE PINNING if the data is sensitive — otherwise anyone
    can proxy the traffic with a self-signed cert in 5 minutes.
 ⑦ ⭐ PERMISSIONS: ask at the moment of use with a reason, never on
    launch. Request the least (photo picker, not full library).
 ⑧ Uploads: check content, cap size, ⭐⭐ STRIP EXIF (photos carry GPS).
 ⑨ ⭐ BIOMETRICS FOR RE-AUTH, not as the only auth.
 ⑩ Clear all local data on logout — ⭐⭐ including the query cache.
```

---

# 6 · ⭐⭐ Cost, battery and data

```
⭐⭐ ON MOBILE, A LOOP COSTS YOU MONEY **AND** COSTS THE USER BATTERY
   AND DATA — AND THEY CAN SEE BOTH IN SETTINGS. A one-star review
   saying "drains my battery" is very hard to recover from.

 ① ⭐⭐ NO API CALL IN render(). Ever.
 ② ⭐ EVERY useEffect THAT FETCHES: correct primitive deps, cleanup.
 ③ ⭐⭐ THE FOCUS-REFETCH LOOP — refetchOnWindowFocus on mobile fires
    on every app foreground. Tab switching becomes a request storm.
 ④ ⭐ EVERY LISTENER REMOVED: AppState, NetInfo, keyboard, navigation,
    dimensions. ⭐⭐ THEY DO NOT UNMOUNT WITH THE SCREEN.
 ⑤ ⭐⭐ NO POLLING IN THE BACKGROUND. Push notifications instead.
 ⑥ Retries: max 3, backoff + jitter, never 4xx except 429.
    ⭐⭐ IN A TUNNEL, EVERY REQUEST FAILS — an uncapped retry loop is
      a battery fire and a bill.
 ⑦ ⭐ RESPECT the user being on cellular — no large downloads without
    asking.
 ⑧ Spend cap + alert on every paid service.
```

---

# 7 · ⭐⭐ Offline is a design decision

```
⭐⭐ DECIDE PER SCREEN, IN ADVANCE. THERE ARE ONLY THREE ANSWERS:

  ① ⭐ WORKS OFFLINE       ⇒ cached, and writes queue for later
  ② ⭐ READS OFFLINE       ⇒ shows cached data with a "last updated"
                             stamp, writes are blocked with a message
  ③ ⭐ NEEDS THE NETWORK   ⇒ a clear offline state, NOT a spinner

  ⭐⭐ AN INFINITE SPINNER ON A LOST CONNECTION IS THE MOST COMMON
    MOBILE BUG THERE IS.

□ Detect offline (NetInfo) and SAY SO
   ⭐ "Connected" is not "the internet works" — captive portals resolve
     DNS and block everything. Test a real request, not the flag.
□ ⭐⭐ QUEUED WRITES ARE IDEMPOTENT. They will be sent twice.
□ Show what is unsynced. ⭐ Never pretend it saved when it did not.
□ Handle the app being killed with a queue still pending
```

---

# 8 · ⭐⭐ The OS can kill you at any moment

```
□ ⭐⭐ SAVE DRAFT STATE AS THE USER TYPES, not on submit.
   ⭐ Android kills a backgrounded app freely. Coming back to an empty
     form loses everything.
□ Handle AppState background/foreground — refresh stale data on return,
   ⭐ but do not stampede every query at once
□ ⭐ A LONG TASK MUST SURVIVE BACKGROUNDING, or tell the user to stay
□ Deep links work from a COLD START, not just when already running
□ ⭐⭐ TEST: rotate · background 10 minutes · kill and reopen ·
   run out of storage · deny every permission
```

---

# 9 · Which AI tool for which job

| Job | Use |
|---|---|
| **Complex / risky change** | ⭐⭐ **Plan mode** |
| Correctness, simplification | `/code-review` |
| Auth, data, payments, storage, permissions | `/security-review` |
| **Final check on money/auth/data** | ⭐⭐ **Codex second opinion** (§3) |
| Searching an unfamiliar codebase | A search subagent |
| Repo rules for the agent | `/init`, then [CLAUDE-md-template.md](CLAUDE-md-template.md) |
| **Architecture, subtle debugging** | ⭐ The strongest model — do not economise |
| Bulk mechanical edits | A faster model |

---

# 10 · ⭐⭐ Ship checklist — nothing submits until every line is ticked

```
CONTENT
  □ ⭐ NO PLACEHOLDER TEXT — no lorem, no "Feature One", no fake people
  □ Real copy, spell-checked
  □ Every image and icon has an accessibility label

LAYOUT
  □ ⭐⭐ NO HORIZONTAL OVERFLOW on the smallest supported device
  □ ⭐ SAFE AREAS respected — notch, home indicator, status bar
  □ Nothing hidden behind the keyboard — ⭐⭐ TEST EVERY FORM WITH THE
     KEYBOARD OPEN, on a small device
  □ Tap targets ≥ 44pt (iOS) / 48dp (Android), not touching
  □ ⭐ TESTED AT LARGEST SYSTEM FONT SIZE — text must not clip
  □ Landscape does not break (or is locked deliberately)

STATES
  □ ⭐⭐ AN OFFLINE STATE on every screen that needs the network
  □ ⭐ AN EMPTY STATE on every list — AND IT IS TWO STATES:
     never-had-any (+ the action) vs filtered-to-zero (+ clear filter)
  □ ⭐⭐ ERROR STATES that say what happened and what to do, with retry
  □ ⭐⭐ SUCCESS FEEDBACK on every action — silence reads as failure and
     people tap twice
  □ Loading skeletons, not a bare spinner
  □ ⭐ PULL-TO-REFRESH where a list can go stale

PERFORMANCE
  □ ⭐⭐ 60fps ON A LOW-END ANDROID, not on your iPhone
  □ ⭐ COLD START UNDER 2 SECONDS
  □ Lists are virtualised (FlashList/FlatList), never .map() over 500 rows
  □ Images resized and cached — ⭐⭐ NEVER a full-resolution photo in a list
  □ ⭐ APP SIZE CHECKED — every MB costs installs on slow connections

SECURITY
  □ ⭐⭐ ID-SWAP TEST PASSED
  □ ⭐⭐ TOKENS IN SECURE STORE, not AsyncStorage
  □ ⭐ NO SECRETS IN THE BUNDLE — unzip the build and grep it
  □ Permissions requested at point of use, with a reason
  □ Uploads validated, EXIF stripped
  □ Logout clears everything local

STORE
  □ ⭐⭐ CORRECT PAYMENT TYPE — IAP for digital, Stripe for physical.
     ⭐ Getting this wrong is a guaranteed rejection.
  □ Privacy policy URL live and reachable
  □ ⭐ DATA-SAFETY / PRIVACY-NUTRITION FORM MATCHES WHAT YOU ACTUALLY
     COLLECT — including what your SDKs collect
  □ Screenshots for every required size, no placeholder art
  □ ⭐ A DEMO ACCOUNT for review, and it works
  □ Age rating, export compliance, permission usage strings that
     explain WHY

OPERATIONS
  □ ⭐ SENTRY WITH NATIVE SYMBOLICATION — and a real crash tested
  □ ⭐⭐ CRASH-FREE RATE VISIBLE. Target > 99.5%.
  □ ⭐ THE OTA UPDATE PATH TESTED ONCE, END TO END
  □ ⭐⭐ A STAGED ROLLOUT PLANNED — never 100% on day one
  □ ⭐ FORCE-UPDATE MECHANISM EXISTS, for the day you must kill a
     broken version
  □ ⭐⭐ TESTED ON: oldest supported iOS · oldest supported Android ·
     one cheap Android · one small screen · one huge screen
```

---

# 11 · Things you do that I need you to stop doing

```
 ① ⭐⭐ CLAIMING IT WORKS WHEN YOU ONLY RAN THE SIMULATOR.
    ⭐ The simulator has no real network, no real memory pressure,
      no real GPU. Say "simulator only" if that is what you did.
 ② ⭐ ADDING A LIBRARY THAT NEEDS NATIVE CODE without saying so —
    ⭐⭐ THAT ENDS OTA UPDATES FOR THAT CHANGE and forces a store review.
 ③ Inventing a package, an API or a config option.
 ④ Silently changing files outside the task.
 ⑤ Deleting or weakening a test to go green.
 ⑥ ⭐⭐ SWALLOWING AN ERROR so the screen goes blank instead of saying
    what went wrong.
 ⑦ ⭐ IGNORING ANDROID. "Works on iOS" is half a job.
 ⑧ ⭐⭐ AGREEING WITH ME WHEN I AM WRONG. If I ask for something that
    will leak data, drain battery, or get rejected — SAY SO FIRST.
```

---

**Depth on any line above:** [the folder index](README.md) · **Per-project rules:**
[CLAUDE-md-template.md](CLAUDE-md-template.md) · **The full audit:**
[10-Ship-Checklist.md](10-Ship-Checklist.md)
