# 🚦 Ship Checklist — the pre-launch audit

> ⭐⭐ **This is the file that catches what vibe coding misses.** The agent builds the feature and
> stops. It does not add a favicon, write a meta description, split the empty state in two, or check
> that the site does not scroll sideways on a phone. **None of these are hard. All of them are
> invisible until someone else looks.**

**How to use it:** paste a section into the chat and say *"audit the site against this and fix every
failure."* Work section by section — not all at once, or you get a 900-line diff you cannot review.

---

# Section 1 — Content

```
□ ⭐⭐ NO PLACEHOLDER TEXT ANYWHERE
   grep the build for: "lorem", "ipsum", "Feature One", "Your Company",
   "Coming soon", "TODO", "Placeholder", "Example.com", "John Doe"
   ⭐ Also: stock avatars of people who do not exist, and a testimonial
     nobody said. Both are worse than nothing.
□ Real copy on every page, spell-checked
□ Every image has alt text that says what the image IS — not "image"
□ Dates formatted for humans ("3 hours ago", with the exact time on hover)
□ Numbers formatted — £1,249.00, not 1249
□ No text that says something the app cannot actually do
```

> ⭐ **Placeholder text is the single clearest signal that nobody finished.** A reviewer who finds
> "Feature One" stops reading the rest of the page.

---

# Section 2 — ⭐⭐ Layout & horizontal scrolling

```
⭐⭐ HORIZONTAL SCROLLING IS THE MOST COMMON SHIPPED BUG ON THE WEB,
   AND IT IS INVISIBLE ON A DESKTOP.

□ NO HORIZONTAL SCROLL AT ANY WIDTH. Test 320px, 375px, 768px.
□ Find it in one line, in the console:

     document.querySelectorAll('*').forEach(el => {
       if (el.scrollWidth > document.documentElement.clientWidth)
         console.log(el);
     });

⭐ THE SIX CAUSES, IN ORDER OF HOW OFTEN THEY ARE IT:
   ① a fixed width (width: 500px) on a container      ⇒ max-width instead
   ② a long unbroken string — a URL, an email, an id  ⇒ overflow-wrap:anywhere
   ③ an image with no max-width: 100%
   ④ a table not wrapped in a div with overflow-x: auto
   ⑤ 100vw plus padding — ⭐⭐ 100vw INCLUDES THE SCROLLBAR
   ⑥ a negative margin or an absolutely positioned element sticking out

□ ⭐ NEVER "FIX" IT WITH overflow-x: hidden ON body.
   That hides the symptom, breaks position:sticky, and the content is
   still unreachable.
```

```
□ Nothing clipped or cut off on a phone
□ Tap targets ≥ 44×44px, and not touching each other
□ Inputs ≥ 16px font — ⭐ SMALLER AND iOS ZOOMS THE PAGE ON FOCUS
□ Content readable at 200% browser zoom
□ Safe-area insets respected on notched phones
□ Long words and headings wrap instead of overflowing
```

---

# Section 3 — Mobile

```
□ ⭐⭐ A WORKING MOBILE MENU — and "working" means all six:
     ① opens and closes
     ② closes when you navigate
     ③ closes on Escape
     ④ traps focus while open (Tab does not reach the page behind)
     ⑤ returns focus to the button that opened it
     ⑥ the page behind does not scroll while it is open
   ⭐ #4 and #6 are the ones that are always missing.

□ ⭐⭐ TESTED ON A REAL PHONE, ON MOBILE DATA — not devtools emulation.
   ⭐ Devtools does not show you: real touch, real font rendering, real
     network latency, iOS Safari's quirks, or the address bar eating 100vh.
□ Use 100dvh, not 100vh — ⭐ 100vh is wrong on mobile Safari
□ Forms usable one-handed; correct keyboard per input (email, tel, number)
□ Test in Safari on iOS specifically — it is not Chrome
□ Landscape orientation does not break
```

---

# Section 4 — ⭐⭐ States

```
⭐⭐ EVERY SCREEN HAS FOUR STATES. THE AGENT BUILDS ONE.

□ LOADING — a skeleton shaped like the content.
   ⭐ Not a centred spinner replaced by content of a different size —
     that is layout shift, and it is measured against you.
   ⭐ Do not show it for under 200ms. A flash is worse than a wait.

□ ⭐⭐ ERROR — says WHAT HAPPENED and WHAT TO DO. Branch by cause:
     401 ⇒ "Please sign in again"        403 ⇒ "You don't have access"
     404 ⇒ "Not found" + a way back      429 ⇒ "Too many requests, wait"
     5xx ⇒ "Something went wrong on our side" + Retry
     offline ⇒ "You appear to be offline"
   ⭐ AND A RETRY BUTTON. An error with no way forward is a dead end.
   ⭐⭐ NEVER CLEAR A FORM ON ERROR. The most user-hostile thing a page
     can do is discard what someone typed because the server said no.

□ ⭐⭐ EMPTY — AND IT IS TWO STATES, NOT ONE:
     · NEVER HAD ANY  ⇒ explain what goes here + THE BUTTON THAT
                         CREATES THE FIRST ONE
     · FILTERED TO ZERO ⇒ "No results for 'xyz'" + [Clear filters]
   ⭐⭐ SHOWING "No orders yet" TO SOMEONE WITH 500 ORDERS AND A BAD
     FILTER MAKES THEM THINK THEIR DATA IS GONE. It is a real bug, and
     filtering to zero is the first thing a reviewer tries.
   ⭐ Keep the filter bar visible when empty, or they are stranded.

□ ⭐⭐ SUCCESS — EVERY ACTION CONFIRMS VISIBLY.
   ⭐ Silence reads as failure, and people click again — which is how
     you get two orders. The confirmation is not decoration; it is what
     stops the double submit.
   ⭐ Better than a toast: the thing on screen actually changes.
     The badge updates. The row appears. The history gains a line.
```

```
□ ⭐⭐ A REAL 404 PAGE — branded, with a search or a route home.
   ⭐ Not the framework default. Not a blank page.
□ A 500 page that is not a stack trace
□ An offline state if the app is usable offline at all
```

---

# Section 5 — SEO & metadata

```
□ ⭐ A UNIQUE, DESCRIPTIVE <title> PER PAGE
   ✅ "Pricing — Acme"        ❌ "React App", "Home", "Untitled"
   ⭐ Under ~60 characters or Google truncates it.
   ⭐⭐ THE TAB IS HOW PEOPLE NAVIGATE BETWEEN OPEN PAGES. Get it right.

□ ⭐ A META DESCRIPTION PER PAGE — 140–160 characters, WRITTEN.
   ⭐ It does not affect ranking. It affects whether anyone CLICKS.
   ⭐⭐ One description reused across every page is worse than none.

□ ⭐⭐ FAVICON — the full set, not the framework default:
     favicon.ico · apple-touch-icon 180×180 · 192 and 512 PNGs ·
     site.webmanifest
   ⭐ The Vite/Next default logo on a real product is a tell.

□ ⭐⭐ OPEN GRAPH + TWITTER CARD — og:title, og:description,
     og:image (1200×630), og:url, twitter:card=summary_large_image
   ⭐ THIS IS WHAT APPEARS EVERY TIME ANYONE SHARES THE LINK IN A
     MESSAGE. Missing it means a grey box with a URL.
   ⭐ Test with a real paste into WhatsApp/Slack, not a validator.

□ robots.txt — and confirm it does NOT block the site
   ⭐⭐ A staging noindex tag shipped to production is a silent
     catastrophe: the site simply never appears in Google.
□ sitemap.xml, submitted to Search Console
□ Canonical URL on every page
□ One <h1> per page; headings in order, no skipped levels
□ lang attribute on <html>
□ Structured data (JSON-LD) if it is a product, article or local business
```

---

# Section 6 — ⭐⭐ Links & contact

```
□ ⭐⭐ NO BROKEN LINKS — crawl the BUILT site and prove it:
     npx linkinator https://yoursite.com --recurse
   ⭐ Check both internal and external. An external link that 404s is
     still your page looking broken.
□ No link to a page you have not built yet
□ No "#" href on anything that looks clickable
□ Anchor links land in the right place (scroll-padding-top if the header
  is fixed)

□ ⭐ EMAIL IS CLICKABLE:
     <a href="mailto:hello@site.com">hello@site.com</a>
   ⭐ Plain text an address and a phone user has to select, copy and
     switch apps. Most will not.

□ ⭐ PHONE IS CLICKABLE, WITH THE FULL INTERNATIONAL NUMBER:
     <a href="tel:+919876543210">+91 98765 43210</a>
   ⭐⭐ THE href MUST BE +COUNTRYCODE AND DIGITS ONLY — no spaces, no
     brackets, no dashes. The display text can be formatted.

□ External links: target="_blank" rel="noopener noreferrer"
   ⭐ Without noopener the opened page can redirect your tab to a
     phishing page.
□ Social links go to accounts that exist
□ Footer links all resolve
```

---

# Section 7 — Performance & images

```
□ ⭐⭐ IMAGES COMPRESSED AND CORRECTLY SIZED — usually the largest win
     on the whole page:
     · WebP or AVIF, not PNG for photos
       ⭐ PNG 2.1 MB → JPEG 340 KB → WebP 180 KB → AVIF 120 KB.
         SAME IMAGE. 17× for one encoder choice.
     · srcset + sizes so a phone does not download a 4000px file
     · width and height ALWAYS set — ⭐ or the page jumps as they load
     · loading="lazy" BELOW the fold
     · ⭐⭐ EAGER + fetchpriority="high" ON THE HERO — lazy-loading the
       hero makes your main metric WORSE while you think you optimised
     · SVG for icons and logos
□ Fonts: woff2, subset, self-hosted, font-display: swap
   ⭐ Without swap the text is INVISIBLE for up to 3 seconds
□ No render-blocking scripts in <head> — defer them
□ Lighthouse ≥ 90 on the MOBILE profile, on the deployed URL
□ Test on throttled 4G with 6× CPU slowdown, not on your laptop
□ Initial JS bundle under ~200KB gzipped
□ No layout shift as content loads
```

---

# Section 8 — ⭐⭐ Security & money

```
□ ⭐⭐ THE ID-SWAP TEST: log in as user A, change an id in a URL or an
   API call to one belonging to user B. ⭐ MUST return 403 or 404.
   ⭐⭐ THIS IS THE #1 REAL DATA LEAK AND IT TAKES 60 SECONDS TO TEST.
□ curl /.env and /.git/config on the live domain ⇒ both 404
□ grep the production build for secrets:
     grep -rE "sk_live|secret|api[_-]?key|BEGIN PRIVATE" dist/ .next/
□ No service_role or admin key reachable from the browser
□ Rate limits live on every public endpoint — per user AND per IP
□ Auth routes (login, reset, email change) rate-limited hard
□ Payment operations idempotent — double-click cannot charge twice
□ Stripe webhook signature verified before any processing
□ Spend cap + alert on every paid service, alert set BELOW the cap
□ Security headers: CSP, HSTS, X-Content-Type-Options, Referrer-Policy
□ Uploads validated by content, size-capped, EXIF stripped
□ HTTPS everywhere, no mixed content warnings
```

---

# Section 9 — Operations

```
□ ⭐ SENTRY LIVE — and you have TRIGGERED A REAL ERROR AND SEEN IT
   ARRIVE. Installed ≠ working.
□ Source maps uploaded to Sentry, not served publicly
□ Uptime monitor with an alert that reaches your phone
□ Analytics recording the ONE action the site exists for
□ ⭐⭐ ROLLBACK TESTED — not "possible". Actually rolled back once and
   timed it.
□ A deep route hard-refreshes without a 404
   ⭐ The dev server handles it; nginx and S3 do not. You find out when
     someone shares a link.
□ Cookies: Secure, HttpOnly, SameSite — checked in the Application tab
   ⭐ Secure cookies silently do not set over plain HTTP
□ Backups on, and a restore tested once
□ Legal pages if you collect anything: privacy policy, terms, cookie notice
```

---

# The 60-second version

When there is no time for the full audit, these nine catch most of it:

```
① ⭐⭐ ID-swap test — can user A read user B's data?
② ⭐⭐ 320px width — does it scroll sideways?
③ ⭐ Filter a list to zero — which empty state appears?
④ ⭐ Visit /nonsense — is there a real 404?
⑤ ⭐⭐ Share the URL in WhatsApp — is there a preview card?
⑥ ⭐ Look at the browser tab — does it say the page name?
⑦ ⭐ Click the email and phone in the footer — do they open apps?
⑧ ⭐⭐ Turn off the network mid-action — is there an error and a retry?
⑨ ⭐ Open the console — is it clean?
```

---

**Back:** [folder index](README.md) · **The memory:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md)
