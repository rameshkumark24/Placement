# ⚡ Performance — measure, then fix the right layer

> ⭐⭐ **The trap: you optimise the layer you can see.** Your API responds in 40ms and the user waits
> three seconds — because the bundle, the images and a request waterfall are the other 98%. **Measure
> first, and measure the whole wait, not your part of it.**

---

# 1 · ⭐⭐ Where the time actually goes

```
⭐⭐ A TYPICAL "SLOW" PAGE, ACCOUNTED FOR:

  DNS + TCP + TLS ..............   300 ms   ⭐ invisible to you
  HTML (TTFB) ..................   150 ms
  ⭐⭐ JS BUNDLE (600 KB on 4G) .. 1,200 ms   ⭐ THE BIG ONE
  parse + execute ..............   400 ms   ⭐ worse on a cheap phone
  ⭐ YOUR API CALL .............    40 ms   ⭐⭐ ← ALL YOU MEASURED
  ⭐⭐ WATERFALL: 2 chained calls    400 ms
  images + render ..............   300 ms
                                 ─────────
                                  ~2.8 s

  ⇒ ⭐⭐ YOUR 40 ms IS 1.4% OF THE WAIT. Halving it changes nothing.
    The bundle and the waterfall are 57%.
```

| ⭐ Symptom | ⭐⭐ The usual real cause |
|---|---|
| Slow to appear | ⭐⭐ **Network** — bundle, images, waterfall. Not renders. |
| Slow after data loads | Over-rendering, or too many DOM nodes |
| Typing lags | A big form re-rendering expensive siblings |
| Slow under load only | ⭐ **The database** → [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md) |
| Slow for some users only | Their device, their network, or a cold start |

---

# 2 · The three numbers

| Metric | Measures | Good | Usual cause when bad |
|---|---|---|---|
| **LCP** | When the main content appears | < 2.5s | ⭐⭐ A huge hero image, slow TTFB, render-blocking CSS/JS |
| **INP** | Responsiveness to **every** interaction | < 200ms | Long tasks blocking the main thread |
| **CLS** | How much the page jumps | < 0.1 | ⭐ Images with no dimensions; a spinner replaced by taller content |

```
⭐⭐ INP IS MISREAD MOST OFTEN. It is not load time — it is EVERY click,
   tap and keypress across the session, reported near p98.
   ⇒ ⭐ ONE 800ms INTERACTION FAILS THE PAGE even if everything else
     is instant. It is a TAIL metric.
```

**Lab vs field:** Lighthouse is reproducible and tells you *why*. Real-user monitoring tells you
*whether*. ⭐⭐ **A perfect Lighthouse score with bad field data just means your test device is not
your users.** Ship the `web-vitals` beacon — it is ~1KB.

---

# 3 · ⭐⭐ Images — usually the biggest single win

```
⭐⭐ THE THREE MISTAKES:

 ① ⭐⭐ A 4000px PHOTO IN A 400px BOX. Megabytes downloaded, decoded
    and thrown away. ⭐ INVISIBLE ON YOUR MACHINE, brutal on a phone.
 ② WRONG FORMAT:
    PNG 2.1 MB → JPEG 340 KB → WebP 180 KB → ⭐ AVIF 120 KB
    ⭐⭐ SAME IMAGE. 17× FOR ONE ENCODER CHOICE.
 ③ NO width/height ⇒ ⭐ THE PAGE JUMPS as images load (CLS)
```

```html
<img srcset="/p-400.webp 400w, /p-800.webp 800w, /p-1600.webp 1600w"
     sizes="(max-width: 600px) 100vw, 50vw"
     src="/p-800.webp" width="800" height="600" alt="What it shows"
     loading="lazy">

<!-- ⭐⭐ THE HERO IS THE EXCEPTION — it defines your LCP -->
<img src="/hero.webp" width="1200" height="600" alt="..."
     fetchpriority="high">   <!-- ⭐ EAGER. NOT lazy. -->
```

> ⭐⭐ **Lazy-loading the hero makes LCP *worse* while you believe you optimised it.** Lazy below the
> fold, eager plus `fetchpriority="high"` above it.

```
□ SVG for icons and logos — it is text, it gzips, it scales
□ ⭐ NEVER RESIZE IN CSS. A 4000px image styled to 400px still
   downloads and decodes 4000px.
□ Use an image CDN (?w=400&fm=webp) or Next's <Image> — every variant,
   no build step
□ ⭐⭐ USER UPLOADS: resize on the server, never serve the original
```

---

# 4 · Fonts

```
□ ⭐⭐ font-display: swap — ONE LINE.
   ⭐ Without it the DEFAULT is invisible text for up to 3 seconds.
     The content arrived and the user cannot read it.
□ woff2 only — ~30% smaller than woff, supported everywhere
□ ⭐ SELF-HOST. Google Fonts costs a separate DNS + TLS + fetch to
   another origin, in your critical path.
□ Subset to the characters you use
□ Two weights, not seven — ⭐ each weight is a separate file
□ ⭐ size-adjust to match the fallback's metrics ⇒ swap without layout shift
□ preload the one font you need above the fold
□ ⭐⭐ A SYSTEM FONT STACK IS ZERO BYTES AND INSTANT. If you cannot say
   what your custom font buys, this is the better engineering decision.
```

---

# 5 · ⭐⭐ The waterfall — the biggest structural win

```
⭐⭐ HTML → JS → API → API → image
   ⭐ EACH ARROW IS A FULL ROUND TRIP THAT COULD NOT START EARLIER.
   ⭐⭐ EACH REQUEST IS INDIVIDUALLY FAST, SO IT IS INVISIBLE IN YOUR
     SERVER METRICS. The page is slow and every endpoint looks fine.

FIXES, IN ORDER OF VALUE:
  ① ⭐ FETCH ON THE SERVER / IN THE ROUTE, not in a mounted component
     ⇒ the fetch starts when NAVIGATION starts, saving a full round trip
  ② ⭐⭐ <link rel="preconnect"> TO YOUR API ORIGIN — ONE LINE,
     usually 100–300ms (DNS + TLS done early)
  ③ hoist chained fetches up and run them in parallel
  ④ preload the LCP image
```

---

# 6 · Bundle

```
□ ⭐ SPLIT BY ROUTE. The login page must not ship the dashboard.
□ Lazy-load heavy, rarely used deps — charts, PDF viewers, editors
□ ⭐⭐ CHECK WHAT THE BIG ONES ARE. Run a bundle visualiser once; you
   will find a 300KB library you use one function from.
   ⭐ moment → Intl.DateTimeFormat (built in). lodash → import the one
     function. axios → fetch plus 40 lines.
□ ⭐ import * as x from "lib" SHIPS THE WHOLE LIBRARY. Import the
   named export.
□ ⭐⭐ SET A BUDGET IN CI — initial JS < 200KB gzipped, and FAIL THE
   BUILD when it is exceeded, so growth is a decision someone made
   rather than drift nobody saw.
□ `defer` every script. ⭐ CSS is always render-blocking — inline the
   critical part if the page is slow to paint.
```

---

# 7 · Caching & the server

```
□ ⭐⭐ HASHED FILENAMES + immutable, 1 year, FOR ASSETS.
   ⭐ index.html is no-cache — it is the manifest pointing at them.
     ⭐⭐ CACHE index.html AND USERS RUN THE OLD APP FOREVER, and no
       deploy will fix it.
□ Static pages at the CDN — the request never reaches your server
□ ⭐ CACHE THE HOT READS in Redis, with a deliberate TTL
□ ⭐⭐ A CACHE KEY FOR PER-USER DATA MUST INCLUDE THE USER.
   ⭐ A key that omits it leaks one user's data to another and
     BYPASSES your authorization — every check passed, the wrong data
     was already cached.
□ ⭐ NEVER CACHE A PERSONALISED PAGE AT THE CDN. That is a security
   incident wearing a performance costume.
```

---

# 8 · The order to fix things

```
⭐⭐ DO THESE IN ORDER. STOP WHEN IT IS FAST ENOUGH.

 ① ⭐⭐ MEASURE — production build, mobile profile, throttled.
    Write the number down.
 ② ⭐ IMAGES — compress, size, format, lazy/eager. Usually the
    biggest single win and the lowest risk.
 ③ ⭐⭐ THE WATERFALL — preconnect, and move fetches up or server-side
 ④ ⭐ FONTS — swap, subset, self-host
 ⑤ BUNDLE — route splitting
 ⑥ ⭐⭐ THE DATABASE — indexes, N+1
    (if it is only slow under load, START HERE)
 ⑦ CACHING
 ⑧ ⭐ RE-RENDERS — LAST, and only if you measured it
 ⑨ ⭐⭐ RE-MEASURE AND COMPARE TO STEP ①
```

---

**Back:** [folder index](README.md) · **Scale:** [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md) ·
**Audit:** [10-Ship-Checklist.md §7](10-Ship-Checklist.md)
