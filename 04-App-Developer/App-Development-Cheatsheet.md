# 📱 App Development — Complete Vibe-Coding Cheatsheet

Building and shipping a mobile app with an AI agent, from idea to store listing.

> Read [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/) first — universal security, API-loop and
> agent rules are not repeated here. This file covers what is **mobile-specific**.

| Companion file | Contents |
|---|---|
| [UI-Component-Libraries.md](UI-Component-Libraries.md) | Component kits, icons, animation libraries |
| [Store-Release-Checklist.md](Store-Release-Checklist.md) | Play Store + App Store submission, review rejections |

---

## What makes mobile different from web

Read this before anything else — these five facts drive every decision below.

1. **You cannot hotfix.** A web bug is fixed in 3 minutes. An App Store review takes 1–3 days.
   Build the kill switch *before* you need it.
2. **The network is hostile.** Users are on 3G, in lifts, on trains. Every request will fail
   sometimes. Offline behaviour is a feature, not an edge case.
3. **Nothing in the app bundle is secret.** Anyone can unzip an APK. API keys shipped in the app
   are public keys — treat them that way.
4. **Battery and data are the user's, not yours.** A polling loop that's merely wasteful on web
   gets your app uninstalled on mobile.
5. **The store is a gatekeeper.** Apple and Google reject apps for reasons that have nothing to do
   with whether the code works. See [Store-Release-Checklist.md](Store-Release-Checklist.md).

---

## 📋 Contents

1. [Pick the stack](#1-pick-the-stack)
2. [Before you prompt](#2-before-you-prompt)
3. [Screen inventory](#3-screen-inventory)
4. [Build order that works](#4-build-order-that-works)
5. [Mobile-specific API safety](#5-mobile-specific-api-safety)
6. [Mobile-specific security](#6-mobile-specific-security)
7. [Offline & state](#7-offline--state)
8. [Permissions & platform behaviour](#8-permissions--platform-behaviour)
9. [Push notifications & deep links](#9-push-notifications--deep-links)
10. [Performance](#10-performance)
11. [Testing on real devices](#11-testing-on-real-devices)
12. [Release & post-launch](#12-release--post-launch)

---

## 1. Pick the stack

| Need | Default choice | Alternative | Notes |
|---|---|---|---|
| Framework | **React Native + Expo** | Flutter | Expo is the most agent-friendly: TS, huge training data, one codebase |
| Language | TypeScript | Dart (Flutter) | |
| Navigation | **Expo Router** | React Navigation | File-based, matches Next.js mental model |
| Styling | **NativeWind** (Tailwind for RN) | StyleSheet | Agents write Tailwind far more reliably |
| Components | **React Native Reusables** / Tamagui | gluestack | shadcn-style, copy-in |
| Server state | **TanStack Query** | RTK Query | Handles retry/cache/offline — critical on mobile |
| Local state | Zustand | Redux Toolkit | |
| Backend | **Supabase** | Firebase | Postgres + auth + storage + realtime |
| Secure storage | `expo-secure-store` | Keychain/Keystore | **Never** AsyncStorage for tokens |
| Local DB | `expo-sqlite` / WatermelonDB | MMKV | For offline-first |
| Push | `expo-notifications` + FCM/APNs | OneSignal | |
| Builds | **EAS Build** | Fastlane | Cloud builds, no local Xcode needed |
| OTA updates | **EAS Update** | CodePush | Your kill switch — set this up early |
| Errors | Sentry (RN SDK) | Bugsnag | Must handle native crashes too |
| Analytics | PostHog / Firebase Analytics | Amplitude | |

**Why Expo over bare React Native:** EAS Build means you can ship an iOS app without a Mac, and
EAS Update means you can push a JS-only fix without a store review. That second point is worth
more than any other tooling decision on this list.

**When to choose Flutter instead:** heavy custom animation, or you want a single rendering engine
guaranteeing pixel-identical output on both platforms.

---

## 2. Before you prompt

- [ ] Problem in one sentence; single core job defined
- [ ] Decided: iOS, Android, or both at launch
- [ ] Minimum OS versions chosen (iOS 15+ / Android 8+ is a sane 2026 default)
- [ ] Every screen listed with its route
- [ ] Navigation structure drawn (tabs? stack? drawer?)
- [ ] Design tokens chosen — colour, type scale, spacing, radius
- [ ] Backend schema + ERD
- [ ] **API contract written** — mobile clients are hard to update, so the contract must be
      versioned and backward-compatible from day one
- [ ] Auth & roles matrix
- [ ] Offline behaviour decided per screen (works offline / read-only / blocked)
- [ ] `CLAUDE.md` in the repo ([template](../00-Vibe-Coding-Core/AI-Agent-Rules-Template.md))
- [ ] `git init` + first commit **before** the first prompt
- [ ] Apple Developer account (₹8,900/yr) and Google Play account ($25 one-time) — register early,
      approval takes days
- [ ] App name availability checked on both stores

> **API versioning matters more on mobile.** Users run old versions for months. Never make a
> breaking change to an endpoint an old build still calls — add `/v2`, and keep `/v1` alive.

---

## 3. Screen inventory

### Auth flow
- [ ] Splash / launch screen
- [ ] Onboarding carousel (skippable)
- [ ] Login · Signup · Forgot password · Reset password
- [ ] OTP / email verification
- [ ] Biometric unlock (Face ID / fingerprint)
- [ ] Permission priming screens (explain *before* the OS dialog)

### Core app
- [ ] Home / feed — with designed empty state
- [ ] Detail screen
- [ ] Create / edit form
- [ ] Search + filters
- [ ] Profile
- [ ] Settings — notifications, theme, language, **account deletion**
- [ ] Notifications list

### System states — build these for every screen
- [ ] Loading (skeleton, not a spinner where possible)
- [ ] Empty
- [ ] Error, with a retry button
- [ ] **Offline banner** — web doesn't need this; mobile always does
- [ ] Pull-to-refresh
- [ ] Pagination / infinite scroll with a bounded page size

### Commerce (if applicable)
- [ ] Product list · detail · cart · checkout · order tracking
- [ ] Payment sheet (Razorpay / Stripe native SDK — **not** a webview)
- [ ] In-app purchase flow if selling digital goods (**mandatory** on iOS — see store checklist)

---

## 4. Build order that works

```
1. Schema + migrations              → verify in the DB GUI
2. Auth + secure token storage      → verify token survives app restart
3. RLS policies                     → verify account B can't read account A
4. API layer + typed client         → verify with curl before any UI
5. Navigation shell (tabs/stack)    → empty screens, but routing works
6. Design system + shared components
7. Core feature screen
8. Offline + error states
9. Push notifications + deep links
10. Analytics + Sentry
11. Store assets + release
```

**Get a build onto a real device by day two.** Simulators hide font rendering, keyboard behaviour,
safe-area issues, performance, and every permission dialog. An app that only ever ran in a
simulator is not a tested app.

---

## 5. Mobile-specific API safety

> Universal rules + code: [API-Safety-and-Cost-Control.md](../00-Vibe-Coding-Core/API-Safety-and-Cost-Control.md)

Mobile amplifies loop bugs, because flaky networks trigger retries and users background/foreground
the app constantly.

### The Flutter trap

```dart
// 💀 CATASTROPHIC — build() runs on every frame. This is an infinite API loop.
@override
Widget build(BuildContext context) {
  fetchData();                       // NEVER call an API from build()
  return Scaffold(...);
}

// ✅ Fetch once, in initState, or use FutureBuilder with a stored future
class _MyScreenState extends State<MyScreen> {
  late final Future<Data> _future;

  @override
  void initState() {
    super.initState();
    _future = fetchData();           // created once, not on every rebuild
  }

  @override
  Widget build(BuildContext context) =>
      FutureBuilder(future: _future, builder: ...);   // not fetchData() inline
}
```

### The React Native traps

```jsx
// 💀 Refetches every time the screen regains focus — including every back-navigation
useFocusEffect(() => { fetchData(); });

// ✅ Bounded: only refetch if data is actually stale
useFocusEffect(
  useCallback(() => {
    if (Date.now() - lastFetch > 60_000) fetchData();
  }, [lastFetch])
);
```

```jsx
// 💀 AppState listener added on every render, never removed
useEffect(() => {
  AppState.addEventListener('change', refetchEverything);
});

// ✅ One listener, removed on unmount
useEffect(() => {
  const sub = AppState.addEventListener('change', handleChange);
  return () => sub.remove();
}, []);
```

### Mobile-specific rules

- [ ] **No API call inside `build()` (Flutter) or the render body (RN)** — ever
- [ ] `useFocusEffect` / `onResume` refetches are time-gated, not unconditional
- [ ] Every listener (`AppState`, network, keyboard, location) is removed on unmount
- [ ] Retries capped at 3 with exponential backoff **and jitter** — a tunnel exit means thousands
      of users retry simultaneously
- [ ] Requests cancelled on screen unmount (`AbortController` / Dio `CancelToken`)
- [ ] Background sync uses the OS scheduler (WorkManager / BGTaskScheduler), never a `setInterval`
- [ ] Polling stops when the app is backgrounded
- [ ] Infinite scroll has a guard so it can't fire twice for the same page
- [ ] Pull-to-refresh is debounced and disabled while a refresh is in flight
- [ ] Image loading uses a caching library (`expo-image`), not a raw fetch per render
- [ ] **Server-side rate limits assume a buggy client** — you cannot patch a released app quickly

```ts
// TanStack Query config that suits mobile
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 2,
      retryDelay: attempt => Math.min(1000 * 2 ** attempt, 10_000),
      staleTime: 60_000,
      refetchOnWindowFocus: false,   // on mobile this fires constantly
      networkMode: 'offlineFirst',
    },
  },
});
```

---

## 6. Mobile-specific security

> Universal list: [Security-Checklist.md](../00-Vibe-Coding-Core/Security-Checklist.md)

### The bundle is public

Anyone can extract your APK/IPA and read every string in it.

- [ ] **No secret keys in the app bundle** — not in code, not in `.env`, not in `app.json`.
      `EXPO_PUBLIC_*` variables are compiled into the binary and are fully readable.
- [ ] Anything requiring a secret goes through **your** backend, which holds the key
- [ ] Third-party keys that must ship (Maps, analytics) are restricted by bundle ID / SHA
      fingerprint in that vendor's console
- [ ] Verify before release: unzip the build and grep for `sk-`, `secret`, `password`

### Token storage

- [ ] Tokens in `expo-secure-store` / Keychain / Keystore — **never** AsyncStorage or
      SharedPreferences, which are plain text on a rooted device
- [ ] Short access-token expiry with refresh rotation
- [ ] Tokens cleared on logout, including from memory
- [ ] Biometric gate on re-entry for anything financial

### Transport & platform

- [ ] HTTPS only; cleartext traffic disabled (`usesCleartextTraffic=false` on Android,
      ATS enabled on iOS)
- [ ] Certificate pinning if the app handles money or health data
- [ ] Root/jailbreak detection for high-value apps (a speed bump, not a wall)
- [ ] Code obfuscation enabled for release builds (ProGuard/R8 on Android)
- [ ] Deep link parameters validated — a malicious link must not navigate into an authenticated
      screen or trigger an action
- [ ] WebViews: `javaScriptEnabled` only if required, URL allowlist enforced
- [ ] Screenshot blocking on sensitive screens (`FLAG_SECURE`) if handling payments
- [ ] Clipboard not used for OTPs or tokens
- [ ] Sensitive data not written to logs in release builds

### Server side stays the same

Every rule from the core checklist still applies — and matters more, because a mobile client is
fully under an attacker's control. **All authorization is server-side.** Assume every request could
be forged by a modified client.

---

## 7. Offline & state

Decide per screen: **works offline / read-only offline / blocked offline.** Write it down.

- [ ] Network status detected and surfaced (`@react-native-community/netinfo`)
- [ ] Offline banner shown, non-blocking
- [ ] Reads served from cache when offline (TanStack Query persistence, or SQLite)
- [ ] Writes queued and replayed when connectivity returns
- [ ] Queued writes are **idempotent** — replay must not double-create
- [ ] Conflict resolution decided (last-write-wins is fine, but decide deliberately)
- [ ] Optimistic UI rolls back visibly on failure
- [ ] App state restored after the OS kills a backgrounded app
- [ ] Deep link into a killed app lands on the right screen with the back stack intact

---

## 8. Permissions & platform behaviour

- [ ] **Prime before you prompt** — a screen explaining *why* precedes every OS permission dialog.
      A denied permission is often permanent.
- [ ] Every permission has a graceful denied path — the app must still function
- [ ] Purpose strings written for iOS (`NSCameraUsageDescription` etc.) — vague ones get rejected
- [ ] Android 13+ notification permission requested explicitly
- [ ] Only request permissions you actually use — unused ones cause store rejections
- [ ] Safe areas respected (notch, dynamic island, gesture bar)
- [ ] Keyboard avoidance on every form
- [ ] Back button / back gesture handled on Android (and doesn't exit mid-flow)
- [ ] Dark mode supported, or explicitly locked to one theme
- [ ] Dynamic font sizes respected (accessibility text scaling)
- [ ] Landscape handled or locked deliberately
- [ ] Tested on a small screen (SE-class) and a large tablet

---

## 9. Push notifications & deep links

- [ ] Push token registered and stored against the user, refreshed on change
- [ ] Permission primed with a value proposition, not requested on first launch
- [ ] Notification tap navigates to the right screen, from a cold start too
- [ ] Deep links / universal links configured and verified on both platforms
- [ ] Notification preferences in settings — and honoured server-side
- [ ] No PII in the notification body (it shows on a lock screen)
- [ ] Silent/background pushes rate-limited — they wake the device and drain battery
- [ ] Badge counts cleared correctly
- [ ] Tested: app foregrounded, backgrounded, and fully killed

---

## 10. Performance

Targets: **cold start < 2s · 60fps scrolling · APK/IPA under 50MB**

- [ ] Lists use `FlashList` or `FlatList` with `keyExtractor` — never `.map()` over a long array
- [ ] `getItemLayout` / `estimatedItemSize` provided for long lists
- [ ] Images cached and correctly sized (`expo-image`); never load a 4000px image into a 100px thumb
- [ ] Animations on the native driver (`useNativeDriver: true` / Reanimated)
- [ ] Heavy work off the JS thread
- [ ] Re-renders profiled — `React.memo` / `useCallback` where a list row re-renders on every keystroke
- [ ] Hermes enabled
- [ ] Bundle size checked; unused assets and fonts removed
- [ ] Splash screen hidden as soon as the first screen is ready
- [ ] Tested on a **low-end Android**, not just a flagship — that's what most users have
- [ ] Battery drain checked over 30 minutes of normal use

---

## 11. Testing on real devices

- [ ] Real iPhone **and** real Android — simulators lie
- [ ] One low-end Android (the majority device in India)
- [ ] Small screen + large tablet
- [ ] Slow 3G and complete offline
- [ ] Airplane mode toggled mid-request
- [ ] App killed and relaunched mid-flow
- [ ] Incoming call during a payment
- [ ] Permission denied paths for every permission
- [ ] Dark mode and largest accessibility font size
- [ ] Fresh install **and** upgrade-over-previous-version (migration bugs live here)
- [ ] Double-tap on every submit and payment button
- [ ] Deep link from a cold start
- [ ] TestFlight / Play Internal Testing with 3–5 real users before public release

---

## 12. Release & post-launch

> Full submission detail: **[Store-Release-Checklist.md](Store-Release-Checklist.md)**

- [ ] Version and build number bumped
- [ ] Release build tested — **not** the debug build (different perf, different crashes)
- [ ] Sentry receiving native crashes with symbolication/dSYMs uploaded
- [ ] **EAS Update / CodePush configured** — your only fast fix path
- [ ] Feature flags / remote kill switch for risky features
- [ ] Forced-update mechanism for when a server change breaks old clients
- [ ] Store listing assets ready (icon, screenshots, description, privacy policy URL)
- [ ] Privacy nutrition label (Apple) / Data safety form (Google) filled honestly
- [ ] Account deletion flow in-app — **both stores require this**
- [ ] Staged rollout on Play Store (start at 10–20%)
- [ ] Crash-free rate watched for 48h — target > 99.5%
- [ ] Store reviews monitored and replied to
- [ ] Spend and API usage watched daily for the first week

---

## Workflow summary

```
Plan → Document (PRD, schema, versioned API contract) → CLAUDE.md → git init
  → Schema → Auth + secure storage → RLS → API → Navigation shell
  → Core screens → Offline + error states → Push + deep links
  → Security pass → Performance pass on a low-end device
  → Real device testing → Store assets → Internal test
  → Staged rollout → Monitor crash-free rate → Iterate via OTA
```
