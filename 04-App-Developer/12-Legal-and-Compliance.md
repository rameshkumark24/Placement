# ⚖️ Legal & Compliance — and the store is your regulator

> ⭐⭐ **This is not legal advice. It is the checklist of what to have.** On mobile there is a second
> layer the web does not have: **Apple and Google enforce a lot of this themselves, and their
> enforcement is a rejection or a removal** — which is faster and more painful than a legal letter.

---

# 1 · ⭐⭐ The four documents

```
 ① ⭐⭐ PRIVACY POLICY — REQUIRED BY BOTH STORES, AT A PUBLIC URL.
    ⭐ A 404 is an instant rejection. Check it on the live domain.
    Must cover: what you collect · why · who you share with
    (⭐⭐ INCLUDING SDKs — Sentry, analytics, ads) · retention ·
    deletion · contact.
 ② ⭐ TERMS OF SERVICE — acceptable use, liability, termination.
    ⭐⭐ AND IF YOU HAVE USER-GENERATED CONTENT, APPLE **REQUIRES** AN
    EULA WITH A NO-TOLERANCE POLICY FOR ABUSIVE CONTENT. §4.
 ③ ⭐ CONSENT / TRACKING NOTICE — §2
 ④ ⭐ REFUND POLICY — ⭐⭐ NOTE: FOR IAP, REFUNDS ARE HANDLED BY THE
    STORE, NOT BY YOU. Say so, and point people to the right place.
    For Stripe purchases the policy is yours.

□ ⭐ REACHABLE FROM INSIDE THE APP, not only from the store listing
□ ⭐⭐ GREP THEM FOR PLACEHOLDERS — "[COMPANY NAME]", "[YOUR", "lorem"
```

---

# 2 · ⭐⭐ Tracking consent — mobile has its own rules

```
⭐⭐ iOS: APP TRACKING TRANSPARENCY (ATT)
  ⭐ If you track a user ACROSS other companies' apps or websites —
    which most ad SDKs do — you must show the ATT prompt and get
    permission FIRST.
  ⇒ ⭐⭐ TRACKING BEFORE THE PROMPT IS A REJECTION AND A REMOVAL RISK.
  ⇒ ⭐ MOST PEOPLE SAY NO. Design as if you have no ad attribution.

⭐⭐ ANDROID: the advertising ID has its own declaration and rules.

⭐⭐ EU/UK: consent must be given BEFORE non-essential tracking loads.
  ⭐ Reject must be as easy as accept. Pre-ticked is not consent.

⇒ ⭐⭐ THE SIMPLEST COMPLIANT ANSWER IS THE SAME AS ON WEB:
  DON'T USE CROSS-APP TRACKERS. Then ATT does not apply, the consent
  question mostly disappears, and your privacy form gets shorter.
```

---

# 3 · ⭐⭐ The store privacy forms — where honesty is enforced

```
⭐⭐ APPLE'S PRIVACY NUTRITION LABEL AND GOOGLE'S DATA SAFETY FORM
   MUST MATCH WHAT YOUR APP ACTUALLY DOES.

  ⭐ THE TRAP: **YOUR SDKs COLLECT THINGS YOU DID NOT WRITE CODE FOR.**
    Analytics, crash reporting, ad networks and attribution SDKs each
    collect identifiers, device info, and sometimes location.
    ⇒ ⭐⭐ YOU MUST DECLARE WHAT THEY COLLECT, NOT ONLY WHAT YOU DO.

□ ⭐ LIST EVERY SDK IN THE APP AND READ ITS DATA DISCLOSURE. Write the
   list down — you will need it again every release.
□ ⭐⭐ APPLE ALSO REQUIRES PRIVACY MANIFESTS for certain SDKs and a
   declared reason for certain "required reason" APIs. ⭐ Missing ones
   fail at upload — check the current list before a release.
□ ⭐ A MISMATCH IS A REJECTION, OR A REMOVAL LATER. Later is worse.
□ Re-check the form whenever you add a dependency
```

---

# 4 · ⭐⭐ User-generated content — Apple's hard requirement

```
⭐⭐ IF USERS CAN POST ANYTHING OTHER USERS SEE — text, photos,
   comments, profiles, a chat — APPLE REQUIRES ALL FOUR:

  ① ⭐ A METHOD TO FILTER OBJECTIONABLE CONTENT
  ② ⭐⭐ A WAY TO REPORT CONTENT, with a timely response
  ③ ⭐ A WAY TO BLOCK ABUSIVE USERS
  ④ ⭐ PUBLISHED CONTACT INFO so users can reach you

  ⇒ ⭐⭐ MISSING THESE IS ONE OF THE MOST COMMON REJECTIONS FOR SOCIAL
    FEATURES, AND PEOPLE ARE ALWAYS SURPRISED BY IT — they think of
    moderation as a growth-stage problem. It is a LAUNCH requirement.
  ⇒ ⭐ AND THE EULA MUST STATE A NO-TOLERANCE POLICY FOR ABUSIVE
    CONTENT AND USERS.

□ ⭐ A REPORT BUTTON ON EVERY PIECE OF USER CONTENT
□ ⭐ A BLOCK ACTION ON EVERY USER
□ ⭐⭐ SOMEONE ACTUALLY READS THE REPORTS. A report queue nobody opens
   is worse than none — you have promised a process you do not run.
```

---

# 5 · ⭐ Account deletion — required, and specific

```
⭐⭐ IF YOUR APP LETS PEOPLE CREATE AN ACCOUNT, APPLE REQUIRES A WAY TO
   **DELETE** IT FROM INSIDE THE APP. Not "email us". In the app.

□ ⭐ AN IN-APP PATH THAT ACTUALLY DELETES — not "deactivate"
□ ⭐⭐ AND IT MUST REACH EVERY SYSTEM: database · storage · the local
   device · your query cache · analytics · YOUR ERROR TRACKER · your
   email provider.
   ⭐ Most delete buttons remove one row and leave the email in four
     other places.
□ ⭐ TELL THE USER WHAT WILL BE DELETED AND WHAT IS RETAINED (and why
   — invoices often must be kept for tax)
□ ⭐ IF THEY HAVE AN ACTIVE SUBSCRIPTION, tell them how to cancel it —
   ⭐⭐ DELETING THE ACCOUNT DOES NOT CANCEL A STORE SUBSCRIPTION, and
   a user who is still being billed will leave a one-star review.
```

---

# 6 · ⭐⭐ Dependency licences

```
⭐⭐ YOU ADD PACKAGES FAST AND DO NOT READ LICENCES. ONE CAN REQUIRE
   YOU TO OPEN-SOURCE YOUR APP.

  ✅ MIT · Apache-2.0 · BSD · ISC
  ⚠️  LGPL · MPL — ⭐ check the linking/file rules
  ❌ ⭐⭐ GPL / AGPL IN A DISTRIBUTED APP IS A REAL PROBLEM — and
     ⭐ APPLE'S APP STORE TERMS CONFLICT WITH GPL DISTRIBUTION,
     which has had apps removed.

□ ⭐ npx license-checker --failOn "GPL-3.0;AGPL-3.0" IN CI
□ ⭐⭐ ASK THE AGENT FOR THE LICENCE **AND** WHETHER IT ADDS NATIVE
   CODE — two questions, every dependency. Put both in CLAUDE.md.
□ ⭐ ATTRIBUTION: many licences require it. ⭐⭐ SHIP AN
   "OPEN SOURCE LICENCES" SCREEN in settings — it is expected, it is
   cheap, and it is generated automatically by most tools.
```

---

# 7 · ⭐ Content and assets

```
□ ⭐⭐ FONTS — "free" often means free for PERSONAL use. Check it
   covers APP EMBEDDING and commercial use.
□ ⭐ ICONS — most are MIT; some require attribution
□ ⭐ STOCK IMAGES — usually exclude implying endorsement by a
   recognisable person
□ ⭐ AI-GENERATED ASSETS — check the generator's commercial terms;
   ⭐⭐ copyright in purely AI-generated work is unsettled
□ ⭐⭐ NEVER USE ANOTHER BRAND'S NAME, LOGO OR ICON in your app, your
   app icon, your screenshots, or your keywords. ⭐ Store keyword
   abuse with a competitor's brand is a fast removal.
□ ⭐ KEEP AN ASSETS FILE: what · source · licence · attribution
```

---

# 8 · Payments & subscriptions

```
□ ⭐⭐ IAP FOR DIGITAL, NOT-IAP FOR PHYSICAL — the rejection rule
   (→ [00-Stack.md §4](00-Stack.md))
□ ⭐ SUBSCRIPTION SCREENS MUST STATE: price · period · what auto-renews
   · how to cancel. ⭐⭐ BOTH STORES REJECT VAGUE PAYWALLS, and this is
   a very common rejection.
□ ⭐ A LINK TO MANAGE/CANCEL THE SUBSCRIPTION from inside the app
□ ⭐ RESTORE PURCHASES — required by Apple
□ ⭐⭐ FREE TRIALS: state exactly when billing starts and how much.
   Regulators and both stores are actively enforcing this.
□ Validate receipts server-side
```

---

# 9 · The ones that depend on where you are

```
□ ⭐ AGE RATING ANSWERED HONESTLY. ⭐⭐ IF UNDER-13s COULD REALISTICALLY
   USE IT, THE RULES ARE MUCH STRICTER (COPPA and equivalents) — and
   both stores have separate kids programmes with hard requirements.
   ⇒ ⭐ GET ADVICE BEFORE BUILDING, not before launching.
□ ⭐ EXPORT COMPLIANCE — asked at every upload. ⭐⭐ USING HTTPS COUNTS
   AS ENCRYPTION; answer it correctly rather than guessing.
□ ⭐ HEALTH, FINANCIAL, BIOMETRIC OR ID DATA ⇒ this file is not enough
□ ⭐ BUSINESS IDENTITY — some markets require a trading name and
   address; both stores require a real developer identity, and
   ⭐⭐ APPLE REQUIRES A VERIFIED TRADER STATUS FOR APPS DISTRIBUTED IN
   THE EU. Check before you are blocked at release time.
□ Permissions you request must match what you actually use
```

---

# 10 · ⭐⭐ The pre-submission legal check

```
□ ⭐⭐ PRIVACY POLICY URL IS LIVE, PUBLIC, AND REACHABLE IN-APP
□ ⭐ TERMS / EULA EXIST — and include the abuse policy if you have UGC
□ ⭐⭐ THE DATA-SAFETY / PRIVACY FORM MATCHES REALITY, INCLUDING SDKs
□ ⭐ PRIVACY MANIFESTS / REQUIRED-REASON APIs handled (iOS)
□ ⭐⭐ IF UGC: report, block, filter, and contact info — ALL FOUR
□ ⭐⭐ IN-APP ACCOUNT DELETION WORKS AND REACHES EVERY SYSTEM
□ ⭐ ATT PROMPT IF YOU TRACK ACROSS APPS — and nothing tracks before it
□ ⭐⭐ npx license-checker — NO GPL/AGPL
□ ⭐ AN OPEN-SOURCE LICENCES SCREEN IN SETTINGS
□ ⭐ SUBSCRIPTION TERMS STATED ON THE PAYWALL; cancel path linked
□ ⭐ AGE RATING AND EXPORT COMPLIANCE ANSWERED HONESTLY
□ ⭐ EVERY FONT, ICON AND IMAGE HAS A LICENCE YOU HAVE READ
□ No other brand's name or logo anywhere, including keywords
```

---

> ⭐⭐ **The mobile-specific reality: the store is a regulator that acts in days.** A legal problem on
> the web is usually a letter you can respond to. ⭐ **A store problem is your app being unavailable
> while you fix it** — so these checks are worth more here than they look.

---

**Back:** [folder index](README.md) · **Privacy controls:** [05-Security.md §7](05-Security.md) ·
**Audit:** [10-Ship-Checklist.md §7](10-Ship-Checklist.md)
