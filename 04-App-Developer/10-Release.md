# Phase 10 — Release

Apple and Google reject apps for reasons unrelated to whether the code works.

**Gate to pass this phase:** store assets complete, OTA update path verified working, staged
rollout planned, rollback rehearsed.

Budget **1–3 days** for Apple review, **hours to 2 days** for Google. Plan your launch date
backwards from that, and never promise a date that assumes first-time approval.

---

## 1. Pre-submission technical gate

- [ ] Version and build number incremented
- [ ] **Release build tested on a real device** — not the debug build. Different performance,
      different crashes, different behaviour.
- [ ] Debug menus, test accounts, seed routes and console logs removed
- [ ] Production API endpoints baked in — not staging
- [ ] **No secrets in the bundle** — unzip and grep to confirm ([Phase 06](06-Security.md#1-the-bundle-is-public))
- [ ] Sentry live with dSYMs / mappings uploaded
- [ ] Analytics firing in the release build
- [ ] Deep links working from a cold start
- [ ] **Push working in production** — APNs production cert, not sandbox. This breaks silently
      between environments more often than anything else.
- [ ] Payments tested in live/sandbox mode, including restore purchases
- [ ] Cold start < 2s on a mid-range device
- [ ] App size acceptable
- [ ] **Fresh install and upgrade-from-previous-version both tested**
- [ ] `/code-review ultra` and `/security-review` run ([Phase 08](08-Testing-and-Review.md))

---

## 2. Assets

### Icon
- [ ] 1024×1024 PNG, no transparency, no rounded corners (the OS rounds them)
- [ ] **Readable at 48px** — test it; most icons fail here
- [ ] Adaptive icon for Android (foreground + background)
- [ ] No screenshots-as-icon, no "beta" badges

### Screenshots
- [ ] iPhone 6.7" (mandatory) and 6.5"; iPad if you support tablets
- [ ] Android phone + 7"/10" tablet if supported
- [ ] 3–8 per size; **the first two are what people actually see**
- [ ] Real UI only — mockups showing invented features get rejected
- [ ] Text overlays readable in a thumbnail

### Listing copy
- [ ] App name (30 chars both stores)
- [ ] Subtitle (30) / short description (80)
- [ ] Full description — the first 3 lines matter most
- [ ] Keywords (Apple: 100 chars, comma-separated, no spaces)
- [ ] Category and content rating questionnaire
- [ ] Support URL and marketing URL live
- [ ] **Privacy policy URL — publicly reachable, no login.** The most common trivial rejection.

### Optional but effective
- [ ] Preview video (15–30s)
- [ ] Feature graphic 1024×500 (Google)

---

## 3. Legal & privacy — where most rejections happen

- [ ] **Privacy Policy live at a public URL** and linked inside the app
- [ ] Terms & Conditions published
- [ ] **Apple privacy nutrition label** accurate — declare every data type collected, including
      analytics and crash-reporting SDKs
- [ ] **Google Data safety form** accurate and consistent with actual SDK behaviour
- [ ] Declarations match reality — mismatches are found and rejected
- [ ] **Account deletion available inside the app** — mandatory on both stores if you support
      signup. "Contact us to delete" is **not** sufficient.
- [ ] Data deletion URL provided (Google requires a web-accessible route too)
- [ ] DPDP Act 2023 (India) consent notice if handling personal data
- [ ] GDPR handling if EU users — lawful basis, export, erasure
- [ ] Third-party SDK data sharing disclosed
- [ ] Age rating accurate

---

## 4. Apple-specific rejection triggers

| Guideline | Rejection | Fix |
|---|---|---|
| **3.1.1** | Selling digital goods/subscriptions outside IAP | Use IAP (15–30% fee). **Physical goods and real-world services are exempt.** |
| **4.2** | "Minimum functionality" — a repackaged website | Add real native capability: offline, push, camera, biometrics |
| **5.1.1** | Requiring signup for features that don't need an account | Allow browsing without login |
| **5.1.1(v)** | No account deletion | Add in-app deletion |
| **2.1** | Crashes on the reviewer's device | Test the release build on a real device |
| **2.3** | Screenshots don't match the app | Use real screenshots |
| **4.0** | Non-native UI, broken layouts, unreadable text | Respect safe areas and dynamic type |
| **5.1.2** | Permissions without a clear purpose string | Write specific purpose strings |
| **1.2** | User-generated content with no moderation | Add reporting, blocking, a moderation policy |

- [ ] **Sign in with Apple** offered if you offer any other social login (Google, Facebook) — required
- [ ] **Demo account credentials supplied in App Review notes** if anything is behind a login,
      with steps to reach gated features. Missing this is an instant rejection.

---

## 5. Google-specific requirements

- [ ] Target API level meets Google's current requirement (raised annually — check before submitting)
- [ ] **App Bundle (`.aab`)**, not APK
- [ ] **Signing key backed up, or Play App Signing enrolled** — lose it and you can never update
      this app
- [ ] Data safety form completed
- [ ] Sensitive permissions justified (background location, SMS, call log — expect scrutiny and
      often rejection)
- [ ] `QUERY_ALL_PACKAGES` avoided unless genuinely required
- [ ] Ads declaration if the app shows ads
- [ ] **Closed test completed if it's a new personal developer account** (12 testers / 14 days)
- [ ] Financial apps: extra declarations for India, sometimes RBI-related documentation

---

## 6. The OTA path — verify it before you need it

EAS Update lets you push a JS-only fix without a store review. It is your kill switch.

- [ ] **EAS Update configured and tested end to end** — push a trivial change and confirm devices
      receive it. Discovering it doesn't work during an incident is the worst possible time.
- [ ] Update channels mapped to build profiles
- [ ] Rollback to a previous update tested
- [ ] Sentry release/dist tagged per update, so post-OTA stack traces still map
- [ ] Team clear on the limit: **JS only.** Native changes still need a store release.
- [ ] **Feature flags / remote kill switch** for risky features — cheaper than any rollback
- [ ] **Forced-update mechanism live** — an endpoint the app checks on launch, so you can retire
      clients when an API change requires it

---

## 7. Rollout

- [ ] **Staged rollout on Google Play — start at 10–20%.** You can halt it; a full rollout you cannot.
- [ ] Phased release on Apple (7-day automatic ramp)
- [ ] **Crash-free sessions monitored hourly. Halt below 99%.**
- [ ] ANR rate watched (Android)
- [ ] Reviews monitored daily for the first week and replied to
- [ ] Server error rate and latency watched for 48h
- [ ] **Bill watched daily for the first week**
- [ ] Rollback plan ready: halt rollout, or push an OTA fix

---

## 8. Common first-submission rejections, ranked

1. Privacy policy URL missing, broken, or behind a login
2. No in-app account deletion
3. Demo credentials not provided for a gated app
4. Crashes on the reviewer's device (debug build submitted, or an untested OS version)
5. Digital goods sold outside IAP (Apple 3.1.1)
6. "Minimum functionality" — a website in a shell (Apple 4.2)
7. Permissions requested with vague or missing purpose strings
8. Screenshots showing features that don't exist
9. Data safety declaration inconsistent with actual SDK behaviour
10. Placeholder content ("Lorem ipsum", "Coming soon") left in the build

---

## Release gate

- [ ] Release build tested on real devices, fresh install **and** upgrade
- [ ] `/security-review` run
- [ ] No secrets in the bundle
- [ ] `/.env` and `/.git/config` return 404 on the API domain
- [ ] IDOR test passed
- [ ] Store assets complete; privacy policy URL publicly reachable
- [ ] In-app account deletion working
- [ ] Demo credentials in review notes
- [ ] Sentry symbolication verified with a test crash
- [ ] **OTA update path verified working**
- [ ] Forced-update mechanism live
- [ ] Staged rollout configured
- [ ] Rollback rehearsed
