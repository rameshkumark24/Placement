# ⚖️ Legal & Compliance — the checks nobody adds

> ⭐⭐ **This is not legal advice. It is the checklist of what to have**, so that when you do talk to
> someone qualified you are asking a short question instead of a long one. **The items here are the
> ones that get vibe-coded projects into trouble, and an agent will never add any of them.**

---

# 1 · ⭐⭐ The four documents

```
⭐⭐ IF YOU COLLECT **ANYTHING** — an email, an analytics event, a
   cookie — YOU NEED THESE BEFORE LAUNCH, NOT AFTER.

 ① ⭐ PRIVACY POLICY — what you collect · why · who you share it with
    (⭐⭐ INCLUDING YOUR SDKs: Sentry, analytics, the CDN) · how long
    you keep it · how someone deletes it · how to contact you
 ② ⭐ TERMS OF SERVICE — what the service is · acceptable use ·
    liability limits · termination · governing law
 ③ ⭐⭐ COOKIE / TRACKING NOTICE — and in the EU/UK, CONSENT BEFORE
    THE TRACKER LOADS. §2.
 ④ ⭐ REFUND / CANCELLATION POLICY — required in many jurisdictions
    the moment you take money, and Stripe will ask for the URL

□ ⭐ LINKED IN THE FOOTER, ON EVERY PAGE, AND AT SIGNUP
□ ⭐⭐ THE URLS MUST NOT 404. Check them on the live domain — a broken
   privacy link is the single most common compliance failure and it
   takes ten seconds to catch.
□ Dated, with a "last updated"
□ ⭐ WRITTEN FOR YOUR APP, not pasted from a generator and left with
   "[COMPANY NAME]" in it. ⭐⭐ GREP YOUR LEGAL PAGES FOR PLACEHOLDER
   TEXT — this happens constantly.
```

---

# 2 · ⭐⭐ Consent — the part people get wrong

```
⭐⭐ A BANNER THAT SAYS "WE USE COOKIES [OK]" IS NOT CONSENT IN THE
   EU/UK. The rules that actually apply:

  ① ⭐⭐ NON-ESSENTIAL TRACKERS MUST NOT LOAD UNTIL THE USER AGREES.
     ⭐ Loading Google Analytics and THEN showing a banner is the
       violation. The banner is not a disclaimer; it is a gate.
  ② ⭐ REJECT MUST BE AS EASY AS ACCEPT. One click each.
  ③ ⭐ PRE-TICKED BOXES ARE NOT CONSENT.
  ④ Consent must be withdrawable — a link that reopens the choice.
  ⑤ ⭐ YOU MUST BE ABLE TO SHOW WHAT SOMEONE CONSENTED TO, AND WHEN.

⭐ ESSENTIAL COOKIES (your session, CSRF, a load balancer) DO NOT NEED
  CONSENT. Analytics, ads and heatmaps do.

⇒ ⭐⭐ THE SIMPLEST COMPLIANT ANSWER: DON'T USE NON-ESSENTIAL TRACKERS.
  A privacy-first analytics tool with no cookies and no personal data
  often needs no banner at all — and the banner costs you conversions
  anyway.
```

---

# 3 · ⭐ Data rights you must actually be able to perform

```
□ ⭐⭐ DELETE MY ACCOUNT AND MY DATA — a real, working path, not an
   email you might action.
   ⭐ AND IT MUST REACH: your DB · uploaded files · the query/CDN
     cache · your analytics · YOUR ERROR TRACKER · your email
     provider · any backup policy you can state.
   ⇒ ⭐⭐ MOST "DELETE ACCOUNT" BUTTONS DELETE ONE ROW AND LEAVE THE
     PERSON'S EMAIL IN FOUR OTHER SYSTEMS.
□ ⭐ EXPORT MY DATA — a machine-readable file
□ Correct my data
□ ⭐ KNOW WHERE PERSONAL DATA LIVES. Write the list down once:
   database · storage · logs · Sentry · analytics · email provider ·
   support inbox · backups. ⭐⭐ YOU CANNOT COMPLY WITH A REQUEST FOR
   DATA YOU HAVE FORGOTTEN YOU HOLD.
□ ⭐ RETENTION: decide how long you keep things, and actually delete.
   "Forever" is a decision you should make on purpose.
□ ⭐⭐ COLLECT LESS. Every field you do not store is one you never have
   to protect, disclose, export or delete.
```

---

# 4 · ⭐⭐ Dependency licences — the vibe-coding trap

```
⭐⭐ YOU ADD PACKAGES FAST AND YOU DO NOT READ THEIR LICENCES. ONE OF
   THEM CAN REQUIRE YOU TO OPEN-SOURCE YOUR PRODUCT.

  ✅ SAFE FOR COMMERCIAL USE: MIT · Apache-2.0 · BSD · ISC
  ⚠️  ⭐ CHECK: LGPL (linking rules) · MPL (file-level copyleft)
  ❌ ⭐⭐ AGPL — IF YOU RUN IT AS A NETWORK SERVICE, IT CAN REQUIRE
     YOU TO RELEASE YOUR SOURCE. **This is the one that bites**, and
     it is used by some very popular tools.
  ❌ "Source available" / BSL / commercial-only — ⭐ NOT open source,
     even though the code is on GitHub

□ ⭐ RUN A LICENCE CHECK IN CI:
     npx license-checker --summary
     npx license-checker --failOn "AGPL-3.0;GPL-3.0"
□ ⭐⭐ ASK THE AGENT FOR THE LICENCE WHEN IT ADDS A DEPENDENCY.
   Put it in CLAUDE.md: "state the package name, why, AND its licence."
□ ⭐ SELF-HOSTING AN AGPL TOOL FOR YOUR OWN USE IS USUALLY FINE.
   BUILDING YOUR PRODUCT ON IT MAY NOT BE. Know which you are doing.
```

---

# 5 · ⭐⭐ Content and assets — the other licence problem

```
⭐⭐ EVERY IMAGE, ICON, FONT AND SOUND IN YOUR APP CAME FROM SOMEWHERE.

□ ⭐⭐ FONTS ARE THE #1 ACCIDENTAL VIOLATION. "Free" often means free
   for PERSONAL use. ⭐ Check the licence covers WEBFONT EMBEDDING and
   COMMERCIAL use. Google Fonts is safe; a font from a design blog
   frequently is not.
□ ⭐ ICON SETS: most are MIT, some require attribution. Read it once.
□ ⭐⭐ STOCK IMAGES: "free" usually excludes using a recognisable
   person to imply endorsement, and often excludes resale.
□ ⭐ AI-GENERATED IMAGES: check your generator's terms for commercial
   use, and be aware ⭐⭐ COPYRIGHT IN PURELY AI-GENERATED WORK IS
   UNSETTLED IN SEVERAL JURISDICTIONS — you may not be able to stop
   someone copying it.
□ ⭐ NEVER USE A BRAND'S LOGO, NAME OR SCREENSHOT to imply a
   partnership you do not have.
□ ⭐⭐ KEEP AN ASSETS FILE: what · where from · licence · attribution
   required. ⭐ Five minutes now, unanswerable in a year.
```

---

# 6 · ⭐ Email compliance

```
⭐⭐ IF YOU SEND MARKETING EMAIL, THESE ARE LEGAL REQUIREMENTS IN MOST
   MARKETS, NOT ETIQUETTE:

□ ⭐ A WORKING ONE-CLICK UNSUBSCRIBE, honoured promptly
□ ⭐ YOUR REAL IDENTITY AND A POSTAL ADDRESS in the footer
□ ⭐⭐ NO PRE-TICKED MARKETING CONSENT AT SIGNUP. In the EU/UK,
   marketing needs OPT-IN, not opt-out.
□ ⭐ TRANSACTIONAL EMAIL (receipts, password resets) IS DIFFERENT and
   does not need consent — ⭐⭐ BUT DO NOT PUT MARKETING IN IT. Once
   you add a promotion to a receipt, it stops being transactional.
□ Separate the two lists and the two sending domains
```

⭐ **Deliverability (SPF/DKIM/DMARC) is a separate problem** →
[04-Backend.md §10](04-Backend.md).

---

# 7 · Payments & consumer rules

```
□ ⭐ REFUND / CANCELLATION POLICY published and honoured
□ ⭐⭐ PRICES SHOWN WITH TAX WHERE REQUIRED. In the EU/UK, prices to
   consumers are shown INCLUSIVE of VAT.
□ ⭐ SUBSCRIPTIONS: state the renewal price and date · make cancelling
   as easy as subscribing · ⭐⭐ SEND A RENEWAL REMINDER where required.
   ⭐ "Easy to start, hard to cancel" is now specifically regulated in
     several markets.
□ ⭐ SALES TAX / VAT ON DIGITAL GOODS IS BASED ON THE CUSTOMER'S
   LOCATION. ⭐⭐ Use Stripe Tax or an equivalent — this is genuinely
   complicated and getting it wrong accrues quietly.
□ Never store card data. ⭐ Use a hosted checkout and stay out of
   PCI scope.
□ Invoices with the required fields for your jurisdiction
```

---

# 8 · ⭐ Accessibility is a legal requirement, not only a quality one

```
⭐⭐ IN MANY MARKETS, A PUBLIC-FACING SERVICE MUST BE ACCESSIBLE —
   and enforcement is increasing, including in the EU.

□ ⭐ DO THE FOUR THINGS IN [02-UI-System.md §7](02-UI-System.md):
   semantic elements · labels · visible focus · contrast
□ ⭐⭐ TEST WITH THE KEYBOARD. Five minutes, finds most of it.
□ ⭐ DO NOT CLAIM A WCAG LEVEL YOU HAVE NOT AUDITED. An overclaim in
   an accessibility statement is worse than no statement.
□ ⭐ AN ACCESSIBILITY STATEMENT with a contact route is expected in
   some jurisdictions
```

---

# 9 · The ones that depend on where you are

```
□ ⭐⭐ BUSINESS IDENTITY PAGE. Germany requires an Impressum; other
   countries require a trading name, address and company number.
   ⭐ A site selling to consumers usually must say WHO IS SELLING.
□ ⭐ AGE: if under-13s (or under-16 in parts of the EU) could realistically
   sign up, ⭐⭐ THE RULES ARE MUCH STRICTER AND YOU SHOULD GET ADVICE.
   Add an age gate or state a minimum age in the terms.
□ ⭐⭐ HEALTH, FINANCIAL, BIOMETRIC OR GOVERNMENT-ID DATA ⇒ this file
   is not enough. Get advice before you build.
□ Selling into the EU/UK from elsewhere still means their rules apply
□ ⭐ IF YOU PROCESS EU PERSONAL DATA, you may need a documented lawful
   basis and, for some setups, a DPA with each processor you use
```

---

# 10 · ⭐⭐ The pre-launch legal check

```
□ ⭐⭐ PRIVACY POLICY, TERMS, COOKIE NOTICE AND REFUND POLICY EXIST,
   ARE LINKED IN THE FOOTER, AND DO NOT 404 ON THE LIVE DOMAIN
□ ⭐ GREP THE LEGAL PAGES FOR PLACEHOLDERS: "[COMPANY]", "[YOUR",
   "lorem", "example.com"
□ ⭐⭐ NO NON-ESSENTIAL TRACKER FIRES BEFORE CONSENT (check the
   Network tab on a fresh browser profile)
□ ⭐ "DELETE MY ACCOUNT" WORKS, AND YOU KNOW EVERY SYSTEM IT TOUCHES
□ ⭐⭐ npx license-checker — NO AGPL/GPL IN A COMMERCIAL PRODUCT
□ ⭐ EVERY FONT, ICON SET AND IMAGE HAS A LICENCE YOU HAVE READ
□ ⭐ MARKETING EMAIL HAS UNSUBSCRIBE + A POSTAL ADDRESS
□ Subscription cancellation is as easy as signup
□ ⭐ TAX HANDLING DECIDED (Stripe Tax or equivalent)
□ You can name every third party that receives user data
```

---

> ⭐⭐ **When to stop and ask a professional:** you take health, financial, biometric or ID data ·
> children could realistically be users · you are raising money or signing a customer contract ·
> someone has sent you a legal letter. **Everything above is cheap. Those are not, and getting them
> wrong is not cheap either.**

---

**Back:** [folder index](README.md) · **Privacy controls:** [05-Security.md §10](05-Security.md) ·
**Audit:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
