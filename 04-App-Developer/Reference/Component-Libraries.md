# 🎨 Mobile UI Component & Animation Libraries

Pick one component system and stay in it. Mixing kits produces an app that feels stitched together.

---

## React Native / Expo

### React Native Reusables — *start here*
- **URL:** https://rnr-docs.vercel.app
- shadcn/ui ported to React Native. Copy-in components you own, styled with NativeWind.
- **Why:** agents write it reliably, and you can edit any component directly.

### NativeWind
- **URL:** https://www.nativewind.dev
- Tailwind classes in React Native. Removes the StyleSheet boilerplate agents get wrong.

### Tamagui
- **URL:** https://tamagui.dev
- Performance-focused, compiles styles ahead of time. Best if you also target web from the same code.

### gluestack-ui
- **URL:** https://gluestack.io
- Larger prebuilt component set when you want breadth over control.

### React Native Paper
- **URL:** https://reactnativepaper.com
- Material Design. Good when you want to look native-Android by default.

---

## Animation & gestures

| Library | URL | Use for |
|---|---|---|
| Reanimated 3 | https://docs.swmansion.com/react-native-reanimated | All serious animation — runs on the UI thread |
| Gesture Handler | https://docs.swmansion.com/react-native-gesture-handler | Swipes, drags, pan |
| Moti | https://moti.fyi | Declarative animation wrapper over Reanimated |
| Lottie | https://lottiefiles.com | After Effects animations, onboarding, empty states |
| Skia | https://shopify.github.io/react-native-skia | Custom drawing, charts, shaders |

**Rule:** animations must run on the native/UI thread. A JS-thread animation stutters the moment
the app does anything else.

---

## Essential utility libraries

| Need | Library |
|---|---|
| Navigation | `expo-router` |
| Server state | `@tanstack/react-query` |
| Local state | `zustand` |
| Secure storage | `expo-secure-store` |
| Fast key-value | `react-native-mmkv` |
| Local DB | `expo-sqlite`, WatermelonDB |
| Images | `expo-image` (caching, blurhash) |
| Long lists | `@shopify/flash-list` |
| Forms | `react-hook-form` + `zod` |
| Network status | `@react-native-community/netinfo` |
| Bottom sheets | `@gorhom/bottom-sheet` |
| Icons | `lucide-react-native`, `@expo/vector-icons` |
| Date handling | `date-fns` |
| Push | `expo-notifications` |
| Payments (India) | `react-native-razorpay` |

---

## Flutter

| Need | Package |
|---|---|
| State | Riverpod (preferred) or Bloc |
| Navigation | `go_router` |
| HTTP | `dio` (interceptors, cancel tokens, retries) |
| Local DB | `drift` or `isar` |
| Secure storage | `flutter_secure_storage` |
| Images | `cached_network_image` |
| Animation | `flutter_animate` |
| Forms | `flutter_form_builder` |
| Push | `firebase_messaging` |

Widget catalogues: [FlutterGems](https://fluttergems.dev) · [pub.dev](https://pub.dev)

---

## Design assets

| Resource | URL | Purpose |
|---|---|---|
| Mobbin | https://mobbin.com | Real app screenshots — the best source for patterns |
| Figma Community | https://figma.com/community | Free UI kits and device mockups |
| LottieFiles | https://lottiefiles.com | Animations for onboarding and empty states |
| Lucide | https://lucide.dev | Icons |
| unDraw | https://undraw.co | Illustrations for empty states |
| App Icon Generator | https://easyappicon.com | All required icon sizes |
| Screenshot frames | https://screenshots.pro | Store screenshots with device frames |

---

## Platform design references

- **iOS Human Interface Guidelines** — https://developer.apple.com/design/human-interface-guidelines
- **Material Design 3** — https://m3.material.io

Follow the platform, don't fight it. An iOS app that uses Android navigation patterns feels broken
to iOS users, and Apple's reviewers notice.

---

## Mobile UI rules that matter

- Touch targets **44×44pt minimum** (iOS) / 48×48dp (Android)
- Bottom navigation, not top — thumbs reach the bottom of the screen
- Primary actions in the lower half of the screen
- Respect safe areas: notch, dynamic island, home indicator, gesture bar
- Never disable the Android back gesture; handle it
- Skeleton loaders, not spinners, for content that has a known shape
- Every list needs an empty state — it's the first thing a new user sees
- Support dynamic font scaling; test at the largest accessibility size
- Support dark mode, or lock to one theme deliberately and consistently
- Honour `prefers-reduced-motion` / "Reduce Motion" system setting
