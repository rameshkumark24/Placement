# 🏪 Store Release Checklist

Apple and Google reject apps for reasons unrelated to whether the code works. This is the list that
gets you through review the first time.

Budget **1–3 days** for Apple review, **hours to 2 days** for Google. Plan your launch date backwards
from that, and never promise a date that assumes a first-time approval.

---

## Accounts (do this weeks early)

| | Apple | Google |
|---|---|---|
| Cost | ₹8,900 / year | $25 one-time |
| Approval time | Up to a week (longer for organisations) | Usually 48h |
| Org accounts need | D-U-N-S number (can take 2 weeks) | Business verification |

Google also requires **new personal developer accounts to run a closed test with 12+ testers for
14 days** before you can publish publicly. This surprises people and delays launches — start it early.

---

## Assets required

### Icon
- [ ] 1024×1024 PNG, no transparency, no rounded corners (the OS rounds them)
- [ ] Readable at 48px — test it, most icons fail here
- [ ] No screenshots-of-the-app-as-icon, no "beta" badges

### Screenshots
- [ ] iPhone 6.7" (mandatory) and 6.5"; iPad if you support tablets
- [ ] Android phone + 7" and 10" tablet if supported
- [ ] 3–8 per device size; the **first two** are what people actually see
- [ ] Show real UI — mockups with invented features get rejected
- [ ] Text overlays large enough to read in a thumbnail

### Listing copy
- [ ] App name (30 chars Apple / 30 Google) — check availability early
- [ ] Subtitle (30) / short description (80)
- [ ] Full description — first 3 lines matter most
- [ ] Keywords (Apple, 100 chars, comma-separated, no spaces)
- [ ] Category chosen
- [ ] Content rating questionnaire completed
- [ ] Support URL and marketing URL live
- [ ] **Privacy policy URL — publicly reachable, no login.** Most common trivial rejection.

### Optional but effective
- [ ] Preview video (15–30s)
- [ ] Feature graphic 1024×500 (Google)

---

## Legal & privacy — where most rejections happen

- [ ] **Privacy Policy live at a public URL** and linked inside the app
- [ ] Terms & Conditions published
- [ ] **Apple privacy nutrition label** completed accurately — declare every data type you collect,
      including analytics and crash reporting SDKs
- [ ] **Google Data safety form** completed and consistent with what the app actually does
- [ ] Declarations match reality — mismatches are found and rejected
- [ ] **Account deletion available inside the app** — mandatory on both stores if you support signup.
      A "contact us to delete" link is not sufficient.
- [ ] Data deletion URL provided (Google requires a web-accessible route too)
- [ ] DPDP Act 2023 (India) consent notice if handling personal data
- [ ] GDPR handling if EU users — lawful basis, export, erasure
- [ ] Third-party SDK data sharing disclosed
- [ ] Age rating accurate

---

## Apple-specific rejection triggers

| Guideline | What gets you rejected | Fix |
|---|---|---|
| 3.1.1 | Selling digital goods/subscriptions without In-App Purchase | Use IAP. Apple takes 15–30%. **Physical goods and services are exempt** — those use a normal payment SDK. |
| 4.2 | "Minimum functionality" — app is a repackaged website | Add real native capability: offline, push, camera, biometrics |
| 5.1.1 | Requiring signup for features that don't need an account | Allow browsing without login |
| 5.1.1(v) | No account deletion | Add in-app deletion |
| 2.1 | Crashes on the reviewer's device | Test the release build on a real device |
| 2.3 | Screenshots don't match the app | Use real screenshots |
| 4.0 | Non-native UI, broken layouts, unreadable text | Respect safe areas and dynamic type |
| 5.1.2 | Requesting permissions without a clear purpose string | Write specific purpose strings |
| 1.2 | User-generated content with no moderation | Add reporting, blocking, and a moderation policy |

**Sign in with Apple is required** if you offer any other third-party social login (Google, Facebook).

**Demo account:** if any part of the app is behind a login, supply working credentials in App Review
notes — otherwise it's an instant rejection. Include steps to reach the paid/gated features.

---

## Google-specific requirements

- [ ] Target API level meets Google's current requirement (raised annually — check before submitting)
- [ ] App Bundle (`.aab`), not APK
- [ ] Signing key backed up, or enrol in Play App Signing (**lose it and you can never update the app**)
- [ ] Data safety form completed
- [ ] Sensitive permissions justified in the declaration form
      (background location, SMS, call log — expect scrutiny and often rejection)
- [ ] `QUERY_ALL_PACKAGES` avoided unless genuinely required
- [ ] Ads declaration if the app shows ads
- [ ] Closed test completed if it's a new personal developer account (12 testers / 14 days)
- [ ] Financial apps: extra declarations for India, sometimes RBI-related documentation

---

## Pre-submission technical gate

- [ ] Version and build number incremented
- [ ] **Release build** tested on a real device (not debug — different performance and crashes)
- [ ] Debug menus, test accounts, seed routes and console logs removed
- [ ] Production API endpoints (not staging) baked into the release build
- [ ] Crash reporting live with symbolication (dSYM upload / ProGuard mapping)
- [ ] Analytics firing correctly in the release build
- [ ] Deep links working from a cold start
- [ ] Push notifications working in production (APNs production cert, not sandbox)
- [ ] Payments tested in live mode with a real small charge, then refunded
- [ ] No secrets in the bundle — unzip and grep to confirm
- [ ] App size reasonable (< 50MB download where possible)
- [ ] Cold start under 2s on a mid-range device
- [ ] Tested fresh install **and** upgrade from the previous version

---

## Rollout

- [ ] **Staged rollout on Google Play — start at 10–20%.** You can halt it; a full rollout you cannot.
- [ ] Phased release enabled on Apple (7-day automatic ramp)
- [ ] Crash-free rate monitored — target **> 99.5%**. Halt the rollout below 99%.
- [ ] ANR rate watched (Android)
- [ ] Reviews monitored daily for the first week and replied to
- [ ] Server error rate and latency watched for 48h
- [ ] **Bill watched daily for the first week**
- [ ] OTA update path (EAS Update / CodePush) verified working *before* you need it

---

## After launch

- [ ] Forced-update mechanism tested — you will eventually need to retire an old client
- [ ] Old API versions kept alive; never break an endpoint an old build still calls
- [ ] Store listing A/B tested (Google Play Experiments)
- [ ] Review prompts triggered after a success moment, never on launch
- [ ] Release notes written for each update
- [ ] Post-incident notes after the first real incident

---

## Common first-submission rejections, ranked

1. Privacy policy URL missing, broken, or behind a login
2. No in-app account deletion
3. Demo credentials not provided for a gated app
4. Crashes on the reviewer's device (debug build submitted, or an untested OS version)
5. Digital goods sold outside IAP (Apple)
6. "Minimum functionality" — it's a website in a shell (Apple 4.2)
7. Permissions requested with vague or missing purpose strings
8. Screenshots showing features that don't exist
9. Data safety declaration inconsistent with actual SDK behaviour
10. Placeholder content ("Lorem ipsum", "Coming soon") left in the build
