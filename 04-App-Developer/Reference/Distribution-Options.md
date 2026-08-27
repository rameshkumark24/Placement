# Distribution Options — With and Without the Stores

Decide this in [Phase 01](../01-Scope-and-Planning.md), not at release. It changes your
architecture, your update speed, and whether you need an Apple account at all.

---

## The short answer

| | Public distribution without store review? |
|---|---|
| **iOS** | **No.** App Store review is unavoidable for public distribution outside the EU. |
| **Android** | **Yes.** Direct APK download from your own site is fully legitimate. |
| **PWA** | **Yes, both platforms.** No store, no review, instant updates. |

If store review is the thing you want to avoid, **the real answer is usually a PWA**, not a
workaround.

---

## Option 1 — PWA (no store at all)

A web app users install to their home screen. No developer account, no review, no 1–3 day wait, and
you ship fixes in seconds.

**What you get on both platforms**
- Home-screen icon, standalone window (no browser chrome), splash screen
- Offline via service worker
- Push notifications — **including iOS 16.4+**, but only once the user adds it to the home screen
- Instant updates, no review, no version fragmentation

**What you don't get**
- No store listing, so no store discovery — you own all acquisition
- iOS: no background sync, no Bluetooth/NFC, limited storage (evictable), no biometrics via
  native API, no IAP
- Android: broader support, but still no deep native integration
- iOS install flow is awkward — Share → Add to Home Screen. Many users won't find it unprompted.

**Choose a PWA when:** the app is content, forms, dashboards, booking, internal tools, or anything
that's essentially a website with offline support. **Which is most apps.**

**Don't when:** you need camera-heavy processing, background location, Bluetooth, biometrics,
IAP subscriptions, or store discovery as a growth channel.

> Apple guideline 4.2 rejects apps that are "a repackaged website" anyway. If your app would be
> rejected under 4.2, that's a signal it should be a PWA — not a signal to disguise it better.

---

## Option 2 — Android without the Play Store

Fully legitimate and supported. Google designed for it.

### Direct APK download from your own site
- Host the `.aab`-derived APK, link it from your site
- User enables "install unknown apps" for their browser — a one-time prompt
- **Play Protect will warn on first install of an app it hasn't seen.** That's expected behaviour,
  not a fault; the warning softens as install volume builds. It is the main conversion cost of
  this route.
- You handle your own update mechanism — build a version check into the app on day one

### Alternative stores
| Store | Notes |
|---|---|
| **F-Droid** | Open-source only, source-built, strong trust signal |
| **Amazon Appstore** | Fire devices + some Android |
| **Samsung Galaxy Store** | Large reach in India |
| **Huawei AppGallery** | Relevant outside Google-services markets |

### Firebase App Distribution
Testers only, not public. Good for a beta group without Play tracks.

### Managed Google Play (private apps)
Publish privately to specific organisations. Lighter review, invisible publicly. The right answer
for an internal company app.

---

## Option 3 — iOS without the App Store

All of these are legitimate, and all are limited. **None gives you public distribution.**

### TestFlight
- **Internal testers:** up to 100 App Store Connect users — **no review**, builds available in minutes
- **External testers:** up to 10,000 — requires a lighter Beta App Review for the first build of
  each version
- Builds expire after **90 days**
- Genuinely useful for a small real user base, but it is beta infrastructure — you can't run a
  product on it long-term

### Ad Hoc
- Up to **100 devices per device type per membership year**, registered by UDID
- No review
- You collect every tester's device ID manually
- Fine for personal use or a handful of devices; unusable beyond that

### Apple Developer Enterprise Program
- $299/year, **employees of your own organisation only**
- Requires 100+ employees, a legal entity, a D-U-N-S number
- **Heavily policed.** Using it for public distribution gets the certificate revoked, which
  instantly bricks the app on every device that installed it. Companies have lost this over it.
- Not a workaround. Don't treat it as one.

### Custom Apps / Apple Business Manager
- Private distribution to specific organisations that you name
- Lighter review than public App Store
- The correct route for a B2B app sold to known customers

### EU only — alternative marketplaces & web distribution (DMA)
- Since iOS 17.4, EU users can install from alternative marketplaces or directly from a developer's
  website
- **Apple Notarization is still required** — an automated review pass, plus eligibility criteria
- A Core Technology Fee applies above a first-install threshold, and the terms have changed
  repeatedly since launch — **verify the current numbers with Apple before planning around them**
- EU users only. Doesn't help you in India.

---

## Option 4 — Ship to the store, but update without review

This is what most teams actually want, and you already have it configured
([Phase 10](../10-Release.md#6-the-ota-path--verify-it-before-you-need-it)).

**EAS Update / CodePush** pushes JavaScript-only changes straight to installed apps — no review, no
wait. It covers bug fixes, copy changes, UI changes, logic changes.

**It does not cover:** native module changes, permission changes, SDK upgrades, app icon, or
anything in `app.config.ts` that affects the native build. Those still need a store release.

Both stores explicitly permit OTA updates that don't materially change the app's purpose. Using OTA
to ship functionality you deliberately hid from review is what violates the rules — and it's
detectable, because reviewers do re-check live apps.

---

## Building without getting flagged

"Flagged" happens at two levels, and they have different fixes.

### Automated flags

| Trigger | Fix |
|---|---|
| Play Protect warning on a sideloaded APK | Sign consistently; enrol in Play App Signing; build install volume |
| Aggressive obfuscation resembling malware | Use standard R8/ProGuard, not exotic packers |
| Excessive or unused permissions | **Request only what you use** — the single biggest automated flag |
| Background location, SMS, call log | Expect a manual declaration and heavy scrutiny; avoid unless essential |
| `QUERY_ALL_PACKAGES` | Remove it unless you genuinely need it |
| Ad SDKs with poor reputations | Check the SDK's track record before adding |
| Target API level below Google's minimum | Keep current — raised annually |
| Unsigned or debug-signed release build | Ship a properly signed release build |

### Manual review flags

These are the same rejection triggers listed in
[Phase 10](../10-Release.md#8-common-first-submission-rejections-ranked). The top five:

1. Privacy policy URL missing, broken, or behind a login
2. No in-app account deletion
3. No demo credentials for a gated app
4. Crashes on the reviewer's device (a debug build submitted)
5. Digital goods sold outside IAP (Apple 3.1.1)

### Account-level flags — the ones that end you

- Multiple developer accounts after a termination → permanent ban across all of them
- Repeated policy strikes → account suspension
- **Misrepresenting what the app does in review** → termination, not a rejection

> The distinction that matters: **building to pass review is compliance; disguising the app to get
> through review is fraud.** The first is what this whole folder is about. The second gets your
> account terminated, and on iOS also revokes the app on every device that already installed it.
>
> If your app can't pass review as described honestly, the fix is either changing the app or
> choosing a distribution channel that fits it — a PWA, direct Android download, or private
> enterprise distribution. Not disguising it.

---

## Decision guide

| Situation | Route |
|---|---|
| Content, forms, dashboards, booking, internal tools | **PWA** |
| Personal project, few users, Android | **Direct APK** |
| Personal project, few users, iOS | **TestFlight or Ad Hoc** |
| Internal company app | **Managed Google Play + Apple Custom Apps** |
| B2B sold to named organisations | **Apple Custom Apps + Managed Google Play** |
| Consumer app needing discovery, IAP, deep native | **Both stores. No way around it.** |
| Want fast fixes on a store app | **Store + EAS Update** |

---

## What this changes in the phases

- **PWA route:** [Phase 10](../10-Release.md) becomes web deploy — use
  [`03-Web-Developer/10-Ship-Checklist.md`](../../03-Web-Developer/10-Ship-Checklist.md)
  instead. Phases 05, 06 and 09 still apply in full.
- **Direct APK route:** you still need Phase 10's signing, versioning and rollout discipline, plus
  **your own update mechanism** — nothing prompts users to update for you.
- **Store route:** [Phase 10](../10-Release.md) as written.

Whichever you pick, **Phases 05 (API Safety), 06 (Security) and 09 (Observability) are unchanged.**
Nothing about your distribution channel makes a data leak or a runaway loop less expensive.
