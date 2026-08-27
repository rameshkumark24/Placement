# 🧱 Stack & Services — setup, caps, and the OTA question

> ⭐⭐ **Set the spend cap on the day you create the account.** And decide the payment model *before*
> you build — getting IAP vs Stripe wrong is a guaranteed store rejection.

**This is the default stack. If a project uses something else, keep every rule in this folder and
swap the code.**

---

# 1 · The stack

| Layer | Default | ⭐ What it is for |
|---|---|---|
| **Framework** | Expo (React Native) + TypeScript | ⭐⭐ EAS Update (OTA) is the reason — JS fixes in minutes, not days |
| **Navigation** | Expo Router | ⭐ Deep links, and they must work from a cold start |
| **Server state** | TanStack Query + persistence | ⭐ Offline is a cache decision |
| **Secure storage** | `expo-secure-store` | ⭐⭐ Keychain / Keystore. **Tokens go here.** |
| **Fast storage** | MMKV | ⭐ Non-sensitive only. AsyncStorage is slow *and* plain text. |
| **Backend** | Supabase / your API | ⭐ RLS on and forced |
| **Payments** | ⭐⭐ **IAP for digital, Stripe for physical** | §4 — the rejection rule |
| **Errors** | Sentry + native symbolication | ⭐ A trace without symbols is useless |
| **Builds** | EAS Build + EAS Update | ⭐⭐ Know what OTA can and cannot fix |
| **Push** | Expo Notifications | ⭐ Ask at the right moment, once |
| **Lists** | FlashList | ⭐ Never `.map()` a long list |

---

# 2 · Setup order

```
 ① ⭐ GITHUB       repo · .gitignore with .env · secret scanning ·
                  push protection · branch protection
 ② ⭐⭐ SPEND CAPS  §3. Before code.
 ③ ⭐⭐ STORE ACCOUNTS — DO THIS EARLY, IT IS THE LONG POLE:
     Apple Developer ($99/yr) — ⭐ verification can take DAYS
     Google Play ($25 once) — ⭐⭐ AND NEW PERSONAL DEVELOPER ACCOUNTS
       MAY NEED A CLOSED TEST WITH REAL TESTERS BEFORE YOU CAN GO
       PUBLIC. Check the current rule; it has caught many people out.
 ④ EXPO / EAS     project · ⭐ BUILD A DEV CLIENT EARLY (Expo Go is
                  not your app)
 ⑤ SUPABASE       ⭐ RLS ON FROM THE FIRST TABLE
 ⑥ AUTH           ⭐ tokens into secure-store from day one, not later
 ⑦ ⭐ SENTRY       client + native symbolication · ⭐⭐ TRIGGER A REAL
                  CRASH AND CONFIRM A READABLE TRACE
 ⑧ ⭐⭐ PAYMENTS    decide IAP vs Stripe NOW (§4)
 ⑨ ⭐⭐ CLAUDE.md   → [CLAUDE-md-template.md](CLAUDE-md-template.md)
 ⑩ ⭐ A REAL DEVICE BUILD RUNNING BY DAY TWO
```

---

# 3 · ⭐⭐ Spend caps

```
  □ ⭐ EAS BUILD — build minutes run out; know your plan's limit
  □ Supabase — spend cap; ⭐⭐ WATCH EGRESS, it is the surprise
  □ ⭐⭐ ANY AI/LLM API — a monthly hard cap. The easiest to run away.
  □ Push provider limits
  □ Storage egress alert
  □ ⭐ A SEPARATE OR VIRTUAL CARD with a limit for all dev services —
     ⭐⭐ THE LAST LINE THAT ACTUALLY WORKS

  ⭐ SET A HARD CAP **AND** AN ALERT AT ~50% OF IT. The alert is the
    point; the cap is what stops you being ruined.
```

⭐ **Write down the expected monthly cost per service now**, so "the bill tripled" is a fact and not
a feeling.

---

# 4 · ⭐⭐ IAP vs Stripe — decide before you build

```
⭐⭐ THE #1 STORE REJECTION, AND IT IS BINARY:

  DIGITAL goods · subscriptions · in-app content · unlocking features
    ⇒ ⭐ MUST USE APPLE / GOOGLE IAP. 15–30% commission.
  PHYSICAL goods · real-world services · donations to nonprofits
    ⇒ ⭐ MUST **NOT** USE IAP. Stripe or similar.

  ⭐⭐ GETTING IT BACKWARDS IS AN AUTOMATIC REJECTION AND COSTS A
    REVIEW CYCLE EVERY TIME YOU GUESS WRONG.

  ⭐ THE GREY AREAS ARE REAL — a physical service booked in-app, a
    course with a physical component, a marketplace. ⭐⭐ READ THE
    CURRENT GUIDELINES; THEY CHANGE, AND THEY DIFFER BY REGION.

  □ ⭐ VALIDATE RECEIPTS SERVER-SIDE. A client-side "purchased" is
     trivially faked.
  □ ⭐ RESTORE PURCHASES WORKS — Apple requires it.
  □ Idempotency on every payment and webhook.
```

---

# 5 · ⭐⭐ The OTA question

```
⭐⭐ EAS UPDATE CAN CHANGE:  JavaScript · styles · images · most assets
   ⇒ ⭐ A FIX IN FIVE MINUTES.

⭐⭐ IT CANNOT CHANGE:  native modules · permissions · app.json /
   config plugins · the icon · the splash · anything native
   ⇒ ⭐ A NEW BUILD AND A STORE REVIEW. 1–3 DAYS.

⇒ ⭐⭐ SO EVERY "CAN WE ADD LIBRARY X?" IS REALLY "DOES X ADD NATIVE
  CODE, AND WILL I PAY A STORE REVIEW FOR IT?"
  ⭐ MAKE THE AGENT ANSWER THAT BEFORE INSTALLING ANYTHING.

□ ⭐ PREFER EXPO SDK MODULES — already in the build, already vetted
□ ⭐ BATCH NATIVE CHANGES into one build when you must make them
□ ⭐⭐ TEST THE OTA PATH END TO END ONCE, EARLY — not during an incident
□ ⭐⭐ SHIP A FORCE-UPDATE CHECK — a version compared against a server
   value. ⭐ You cannot recall a release. This is the only kill switch.
```

---

# 6 · Environment & config

```
⭐⭐ THERE ARE NO SECRETS IN THE BUNDLE. Anyone can unzip the APK.

  ✅ FINE: your API base URL · a publishable key designed to be public
     (Stripe pk_, Supabase anon with RLS ON) · the Sentry DSN
  ❌ NEVER: service_role · sk_live/sk_test · any paid third-party key ·
     signing keys · database URLs

□ ⭐ EAS SECRETS for build-time values that must not be in the repo —
   ⭐⭐ BUT ANYTHING THE APP READS AT RUNTIME IS STILL IN THE BUNDLE
□ Different config per profile (dev / preview / production)
□ ⭐ VALIDATE CONFIG AT STARTUP — fail loudly on boot
□ ⭐⭐ UNZIP AND GREP THE BUILD BEFORE EVERY RELEASE (→ [05-Security.md](05-Security.md))
```

---

# 7 · Per-service settings that matter

```
EXPO / EAS
 □ ⭐ DEV CLIENT BUILT EARLY — Expo Go has different native modules
 □ Build profiles: development · preview · production
 □ ⭐ VERSION AND BUILD NUMBER AUTO-INCREMENT
 □ ⭐⭐ THE UPDATE CHANNEL MATCHES THE BUILD PROFILE — mismatched
    channels ship a JS update to the wrong binary

SUPABASE
 □ ⭐⭐ RLS ON AND FORCED FROM THE FIRST TABLE
 □ Connection pooler
 □ ⭐ BACKUPS ON, RESTORE TESTED ONCE
 □ Storage private by default; signed URLs

SENTRY
 □ ⭐⭐ NATIVE SYMBOLICATION CONFIGURED AND VERIFIED WITH A REAL CRASH
 □ Release + build id attached
 □ ⭐ PII SCRUBBED — crash reports leave the device

PUSH
 □ ⭐ CREDENTIALS SET UP EARLY (APNs key, FCM) — they take time
 □ Deep link target tested from a cold start
 □ ⭐⭐ CATEGORIES USERS CAN CONTROL IN-APP
```

---

# 8 · Swapping the stack

```
⭐⭐ THE RULES SURVIVE. THE SNIPPETS DO NOT.

  Expo → bare React Native  ⇒ ⭐⭐ YOU LOSE EAS UPDATE UNLESS YOU WIRE
                              IT YOURSELF. That changes your whole
                              incident response.
  Expo → Flutter/native     ⇒ every rule here still applies; only the
                              APIs change
  Supabase → your own API   ⇒ ⭐ YOU NOW OWN THE RLS EQUIVALENT. The
                              app-level filter becomes the ONLY layer.
  Stripe → IAP or back      ⇒ ⭐⭐ RE-READ §4. This is a rejection risk,
                              not a preference.

⭐ WHEN YOU SWAP, WRITE IT INTO CLAUDE.md IMMEDIATELY, or the agent
  keeps generating code for the old stack.
```

---

**Back:** [folder index](README.md) · **Security:** [05-Security.md](05-Security.md) ·
**Release:** [11-Release-and-After.md](11-Release-and-After.md)
