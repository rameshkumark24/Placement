# Phase 07 — Performance

Targets: **cold start < 2s · 60fps scrolling · APK/IPA under 50MB · no measurable battery drain
when idle**

**Gate to pass this phase:** all four met on a **low-end Android**, not a flagship.

> **Test on the device your users actually have.** A flagship hides every performance problem you
> have. In India especially, the median device is mid-range or below.

---

## 1. Lists — where mobile performance dies

```jsx
// 💀 Renders all 5,000 rows at once; janks and eventually crashes
{items.map(item => <Row key={item.id} item={item} />)}

// ✅ Virtualised, only visible rows are rendered
import { FlashList } from '@shopify/flash-list';

<FlashList
  data={items}
  renderItem={({ item }) => <Row item={item} />}
  keyExtractor={item => item.id}
  estimatedItemSize={80}              // required — without it FlashList can't optimise
  onEndReachedThreshold={0.5}
  onEndReached={loadMore}
/>
```

- [ ] `FlashList` (or `FlatList` with `getItemLayout`) for every list — never `.map()` over a long array
- [ ] `estimatedItemSize` provided
- [ ] Row components wrapped in `React.memo`
- [ ] Callbacks passed to rows wrapped in `useCallback` — otherwise every row re-renders on every
      parent render
- [ ] `keyExtractor` returns a stable ID, never an array index
- [ ] Pagination guarded so `onEndReached` can't fire twice for the same page

---

## 2. Images

Usually the single largest performance and data cost in a mobile app.

- [ ] **`expo-image`**, not `Image` — it caches to disk properly
- [ ] **Resized server-side**; never download a 4000px image for a 100px thumbnail
- [ ] Multiple sizes stored, the right one requested per context
- [ ] WebP/AVIF where supported
- [ ] `placeholder` / blurhash so layouts don't jump
- [ ] `contentFit` set explicitly
- [ ] Cache policy set; cache size bounded
- [ ] Off-screen images not loaded

---

## 3. Rendering & the JS thread

React Native has one JS thread. Anything blocking it freezes the entire UI.

- [ ] **Animations on Reanimated / the native driver** (`useNativeDriver: true`) — JS-thread
      animations stutter the moment anything else happens
- [ ] Heavy computation moved off the JS thread, or chunked
- [ ] `React.memo` / `useMemo` / `useCallback` where a profile shows re-renders, not everywhere
      by default
- [ ] Context split so an unrelated update doesn't re-render the tree
- [ ] Large JSON parsing done off the critical path
- [ ] **Hermes enabled**
- [ ] New Architecture enabled (Expo SDK 52+)
- [ ] Profiled with React DevTools Profiler and Flipper/Perf Monitor

---

## 4. Cold start

- [ ] Under **2 seconds** on a mid-range device
- [ ] Splash hidden as soon as the first screen has content — not before, not long after
- [ ] Work deferred out of app launch: analytics init, prefetching, non-critical SDKs
- [ ] Fonts preloaded via `expo-font` so text doesn't flash
- [ ] Auth check fast — read the token from SecureStore, render optimistically, verify in background
- [ ] Initial screen renders from cache, then refreshes

---

## 5. Bundle & app size

- [ ] Under 50MB download where possible — size directly reduces install conversion
- [ ] Unused assets and fonts removed
- [ ] Only the font weights you actually use are bundled
- [ ] Lottie files optimised (they get large fast)
- [ ] Bundle analysed (`npx expo-atlas`)
- [ ] Android App Bundle (`.aab`) so Play ships per-device slices
- [ ] Large optional assets downloaded on demand, not bundled

---

## 6. Network efficiency — the user's data plan

- [ ] **Delta sync, not full re-downloads** ([Phase 03](03-Architecture-and-Data.md#delta-sync))
- [ ] Responses gzipped
- [ ] Only the fields you need requested — named columns, not `select('*')`
- [ ] Pagination bounded
- [ ] TanStack Query `staleTime` set so navigation doesn't refetch constantly
- [ ] Large downloads deferred to Wi-Fi where sensible
- [ ] Prefetch the next screen's data on likely navigation, not everything

---

## 7. Battery

The failure mode users notice and punish with uninstalls.

- [ ] **Polling stops when backgrounded** ([Phase 05](05-API-Safety.md#4-polling--background-behaviour))
- [ ] Location: coarsest accuracy that works, watchers stopped when the screen closes
- [ ] Background tasks via the OS scheduler, respecting Doze
- [ ] No wake locks unless genuinely required
- [ ] Silent push used sparingly — each one wakes the device
- [ ] Animations paused when off-screen
- [ ] **Measured**: 30 minutes of normal use, then check iOS Battery settings / Android Battery usage

---

## 8. Database & backend

- [ ] Indexes on FKs, `user_id`, and `updated_at` (delta sync)
- [ ] N+1 eliminated
- [ ] Server-enforced max page size
- [ ] Connection pooling on (Supabase port 6543)
- [ ] Tested against 100k+ seeded rows
- [ ] p95 API latency measured — mobile latency is already high, your server shouldn't add to it

---

## 9. Measuring

| What | Tool |
|---|---|
| JS thread FPS, re-renders | React DevTools Profiler, Perf Monitor |
| Cold start | Manual stopwatch on a real device; Firebase Performance |
| Bundle size | `npx expo-atlas` |
| Native performance | Xcode Instruments, Android Studio Profiler |
| Battery | iOS Battery settings, Android Battery Historian |
| Crash-free rate | Sentry |

**Measure on the low-end device, before and after.** A change that helps a flagship can hurt a
budget phone.

---

## Phase gate

- [ ] 60fps scrolling on a **low-end Android**
- [ ] Cold start < 2s on a mid-range device
- [ ] Bundle size acceptable
- [ ] Battery measured over 30 minutes of use
- [ ] All lists virtualised
- [ ] Images sized server-side and cached
