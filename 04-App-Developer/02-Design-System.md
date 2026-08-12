# Phase 02 — Design System

**Gate to pass this phase:** tokens in code, four states designed per screen, platform conventions
chosen deliberately.

> Component kits and asset sources: **[Reference/Component-Libraries.md](Reference/Component-Libraries.md)**

---

## 1. Tokens first

Define in `tailwind.config.js` (NativeWind) before any component exists.

- [ ] Colour: primary, secondary, accent, neutral ramp, semantic (success/warning/error/info)
- [ ] **Light and dark values for every token** — mobile users expect dark mode
- [ ] Type scale + font families (loaded via `expo-font`)
- [ ] Spacing scale (4pt or 8pt base)
- [ ] Border radius scale
- [ ] Elevation/shadow scale (iOS shadows and Android elevation differ — define both)
- [ ] Z-index scale for modals, sheets, toasts

**No hex codes in components.** If the agent writes `#3b82f6`, that's a missing token.

---

## 2. Follow the platform

An iOS app using Android navigation patterns feels broken to iOS users — and reviewers notice.

| | iOS | Android |
|---|---|---|
| Back | Swipe from left edge + back button in header | Hardware/gesture back — **must** be handled |
| Navigation | Bottom tabs | Bottom tabs or nav drawer |
| Modals | Sheets sliding up | Dialogs, bottom sheets |
| Typography | SF Pro | Roboto |
| Switches, pickers | Native iOS styling | Material styling |
| Haptics | Expected on key actions | Lighter usage |

References: [iOS HIG](https://developer.apple.com/design/human-interface-guidelines) ·
[Material Design 3](https://m3.material.io)

- [ ] Decided: one shared look, or platform-adaptive components
- [ ] **Android back gesture handled on every screen** — it must never exit mid-flow silently

---

## 3. Layout rules that are mobile-only

- [ ] **Safe areas respected** — notch, dynamic island, home indicator, gesture bar
      (`react-native-safe-area-context`, never hardcoded padding)
- [ ] **Touch targets ≥ 44×44pt (iOS) / 48×48dp (Android)**
- [ ] Primary actions in the **lower half** of the screen — thumbs don't reach the top
- [ ] Bottom navigation, not top
- [ ] Keyboard avoidance on every form (`KeyboardAvoidingView` / `react-native-keyboard-controller`)
- [ ] Scroll views don't trap the keyboard — tapping outside dismisses it
- [ ] Landscape handled or locked deliberately
- [ ] Tested on a small screen (SE-class) **and** a large tablet

---

## 4. The four states — for every screen

| State | What it needs |
|---|---|
| **Loading** | Skeleton matching the content shape |
| **Empty** | Explanation + primary action — a new user's first impression |
| **Error** | Plain-language message + **retry button** |
| **Success** | The content |

Plus, on mobile specifically:

- [ ] **Offline** — a persistent, non-blocking banner
- [ ] **Stale** — showing cached data with a "last updated" indicator
- [ ] **Pull-to-refresh** on every list

---

## 5. Microcopy

- [ ] Button labels are verbs ("Create account", not "Submit")
- [ ] Error messages specific ("No internet connection — your changes are saved and will sync",
      not "Error")
- [ ] Empty state copy explains what this is and what to do first
- [ ] Permission priming copy written for each permission ([Phase 01](01-Scope-and-Planning.md#5-permissions-plan))
- [ ] Destructive actions confirmed; irreversible ones typed-confirmed
- [ ] Offline messages reassure rather than alarm

---

## 6. Accessibility

- [ ] Contrast ≥ 4.5:1 body text
- [ ] `accessibilityLabel` on every icon-only button
- [ ] `accessibilityRole` set correctly
- [ ] **Dynamic font scaling supported** — test at the largest accessibility size, layouts must not
      break. This is far more common on mobile than on web.
- [ ] Screen reader pass (VoiceOver on iOS, TalkBack on Android) on the critical path
- [ ] `prefers-reduced-motion` / "Reduce Motion" honoured
- [ ] Colour never the only carrier of meaning

---

## 7. Motion budget

- [ ] Animations on **Reanimated / the native driver** — a JS-thread animation stutters the moment
      the app does anything else
- [ ] `transform` and `opacity` only
- [ ] 150ms micro-interactions, 300ms transitions
- [ ] Haptics on meaningful actions (`expo-haptics`), sparingly
- [ ] Lottie for onboarding and empty states, not for everything
- [ ] Reduce Motion respected

---

## 8. App icon & splash — needed earlier than you think

- [ ] Icon 1024×1024, no transparency, no rounded corners (the OS rounds them)
- [ ] **Readable at 48px** — test it; most icons fail here
- [ ] Adaptive icon for Android (foreground + background layers)
- [ ] Splash screen matching the first screen's background, so there's no flash
- [ ] Splash hidden as soon as the first screen is ready

---

## Phase gate

- [ ] Tokens in the config, light and dark
- [ ] Platform conventions decided
- [ ] Safe areas and touch targets handled
- [ ] Four states + offline designed for core screens
- [ ] Dynamic font scaling tested
- [ ] Icon and splash produced
