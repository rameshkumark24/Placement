# ⚡ Performance — 60fps on a phone you would not buy

> ⭐⭐ **Your iPhone is not the test.** The device that decides your reviews is a three-year-old
> mid-range Android with 4GB of RAM, a full storage volume, and a slow network. **Everything janks
> there first.**

---

# 1 · ⭐⭐ The three numbers

| Metric | Target | ⭐ Why it matters |
|---|---|---|
| **Cold start** | ⭐ < 2s | The first impression, and the one people quit during |
| **Frame rate** | ⭐⭐ 60fps **on a low-end Android** | Jank is the difference between "premium" and "cheap" |
| **App size** | As small as you can | ⭐ Every MB costs installs on slow connections and expensive data |

```
⭐⭐ MEASURE ON THE WORST DEVICE YOU SUPPORT, NOT THE BEST.
   ⭐ BUY A CHEAP ANDROID. It is the single most useful testing
     purchase you can make, and it will change what you build.
```

---

# 2 · ⭐⭐ Lists — where mobile performance dies

```
⭐⭐ THE #1 CAUSE OF JANK, EVERY TIME.

  💀 {items.map(i => <Row key={i.id} item={i} />)}
  ⇒ ⭐ 500 rows = 500 components mounted at once. The scroll stutters,
    memory spikes, and on Android it can crash outright.

  ✅ FlashList (or FlatList) — ⭐⭐ RENDERS ONLY WHAT IS VISIBLE.
```

```
□ ⭐ estimatedItemSize set (FlashList) — without it the first render
   is wrong and it visibly reflows
□ ⭐⭐ THE ROW COMPONENT IS MEMOISED and does NOT take inline
   functions or object literals as props — ⭐ that defeats the memo
   entirely and you pay the compare on every render
□ keyExtractor is stable — ⭐ never the index
□ ⭐ NO EXPENSIVE WORK IN renderItem. Precompute before the list.
□ ⭐⭐ NO SHADOW ON EVERY ROW ON ANDROID — elevation is expensive at
   scale. Use a border instead.
□ ⭐ PAGINATE. Do not fetch 5,000 rows and virtualise them; fetch 50.
   ⭐⭐ VIRTUALISING A LIST YOU SHOULD HAVE PAGINATED FIXES THE RENDER
     AND NOT THE PAYLOAD, THE PARSE, OR THE MEMORY.
```

---

# 3 · ⭐⭐ Images

```
⭐⭐ NEVER PUT A FULL-RESOLUTION PHOTO IN A LIST.
   ⭐ A 4000px photo decoded into a 100px thumbnail is ~48MB of
     bitmap memory per image. Ten of them crashes a cheap Android.

□ ⭐ RESIZE SERVER-SIDE. Serve a thumbnail for the list and a larger
   one for detail. ⭐⭐ NEVER RESIZE A HUGE IMAGE ON THE DEVICE.
□ Use expo-image (or FastImage) — ⭐ disk cache, memory cache, and
   proper decoding
□ ⭐ SET EXPLICIT DIMENSIONS. Without them the layout jumps.
□ WebP where you can
□ ⭐ PLACEHOLDER OR BLURHASH while loading — a grey box is fine,
   a jumping layout is not
□ ⭐⭐ USER UPLOADS: resize BEFORE upload (saves their data too),
   and strip EXIF
□ Do not preload a whole gallery
```

---

# 4 · Animation

```
□ ⭐⭐ NATIVE DRIVER, ALWAYS — useNativeDriver: true, or Reanimated.
   ⭐ A JS-driven animation runs on the same thread as your logic, so
     it stutters the moment anything else happens. That is the jank
     users describe as "cheap".
□ ⭐ Reanimated for gestures — it runs on the UI thread
□ 150–250ms for UI transitions. ⭐⭐ A slow transition makes a fast
   app feel slow.
□ ⭐ DO NOT ANIMATE layout properties (width/height/top) if you can
   animate transform and opacity instead
□ ⭐ RESPECT reduce-motion
□ Never animate something the user is trying to read or tap
```

---

# 5 · Cold start

```
⭐⭐ WHAT MAKES IT SLOW, IN ORDER:

 ① ⭐⭐ TOO MUCH WORK BEFORE THE FIRST SCREEN — SDK initialisation,
    auth checks, config fetches, feature flags, all serial.
    ⇒ ⭐ SHOW SOMETHING FIRST. Initialise what you need lazily.
 ② ⭐ A HUGE JS BUNDLE — enable Hermes; check what is imported at
    the top level
 ③ ⭐⭐ A BLOCKING NETWORK CALL BEFORE RENDER. ⭐ On a slow network
    that is your cold start. Render the cached state, refresh after.
 ④ Too many fonts/assets loaded up front
 ⑤ ⭐ SYNCHRONOUS STORAGE READS on the main thread — MMKV is fast,
    AsyncStorage is not

□ ⭐ MEASURE IT ON A COLD BOOT ON A CHEAP ANDROID, not a warm restart
□ ⭐⭐ THE SPLASH SCREEN SHOULD HIDE WHEN THE UI IS READY, not on a
   timer — ⭐ a fixed delay is a self-inflicted slow start
```

---

# 6 · Memory

```
□ ⭐ NAVIGATE 50 SCREENS AND WATCH MEMORY. ⭐⭐ FLAT IS CORRECT.
   CLIMBING IS A LEAK — usually a listener (→ [03-App-Rules.md §3](03-App-Rules.md)).
□ ⭐⭐ ANDROID KILLS THE APP WHEN MEMORY IS TIGHT, and the user
   experiences that as "it closed itself".
□ Release large objects when a screen unmounts
□ ⭐ IMAGES ARE USUALLY THE MEMORY PROBLEM — see §3
□ Do not keep every page of a paginated list in memory forever
```

---

# 7 · Network & battery

```
□ ⭐⭐ NO POLLING. Push notifications instead.
□ ⭐ BATCH REQUESTS where you can. Every radio wake costs battery,
   and ⭐⭐ THE RADIO STAYS AWAKE FOR SECONDS AFTER EACH REQUEST —
   so ten spaced-out small requests cost far more than one batch.
□ ⭐ CACHE AGGRESSIVELY. The cheapest request is the one you skip.
□ ⭐⭐ RESPECT CELLULAR — no large downloads without asking
□ Compress payloads; ⭐ do not send fields the screen does not use
□ ⭐ STOP BACKGROUND WORK WHEN THE APP BACKGROUNDS
□ ⭐⭐ CHECK BATTERY USAGE IN SETTINGS AFTER AN HOUR OF REAL USE.
   ⭐ Users check this, and a bad number becomes a one-star review.
```

---

# 8 · App size

```
□ ⭐ CHECK IT. Then check what is in it — most projects have one
   surprise: a font pack, an icon set, an unused SDK.
□ ⭐⭐ ENABLE HERMES + PROGUARD/R8 + RESOURCE SHRINKING on Android
□ Ship App Bundles (AAB), not universal APKs — ⭐ users download only
   their architecture
□ ⭐ AUDIT DEPENDENCIES FOR SIZE. A native SDK you use one function
   from is the usual culprit.
□ Do not bundle assets you fetch at runtime anyway
□ ⭐⭐ EVERY MB MATTERS in markets with expensive data — and those are
   often the markets with the most users
```

---

# 9 · The order to fix things

```
 ① ⭐⭐ MEASURE ON A CHEAP ANDROID. Write the numbers down.
 ② ⭐ LISTS — virtualise, memoise the row, remove inline props
 ③ ⭐⭐ IMAGES — resize server-side, cache, explicit dimensions
 ④ ⭐ ANIMATION — move to the native driver
 ⑤ COLD START — defer work, render cached state first
 ⑥ ⭐ MEMORY — find the leaked listener
 ⑦ NETWORK — batch, cache, stop polling
 ⑧ ⭐⭐ RE-MEASURE AND COMPARE TO STEP ①
```

---

**Back:** [folder index](README.md) · **Rules:** [03-App-Rules.md](03-App-Rules.md) ·
**Audit:** [10-Ship-Checklist.md §5](10-Ship-Checklist.md)
