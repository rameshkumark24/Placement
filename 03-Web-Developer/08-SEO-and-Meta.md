# 🔍 SEO & Metadata — the ten minutes that decide whether anyone finds it

> ⭐⭐ **Every item here takes minutes and none of them are visible to you.** They are visible to
> Google, to WhatsApp, to the browser tab, and to anyone who shares your link. **An agent never adds
> them unless asked.**

---

# 1 · ⭐ Page titles

```
⭐⭐ THE TAB IS HOW PEOPLE NAVIGATE BETWEEN OPEN PAGES. And the title is
   the biggest text in a Google result.

  ❌ "React App"  ·  "Home"  ·  "Untitled"  ·  the same title everywhere
  ✅ "Pricing — Acme"
     "Order #4471 — Acme"          ⭐ dynamic pages get dynamic titles
     "Noise-cancelling headphones — Acme"

□ UNIQUE PER PAGE
□ ⭐ UNDER ~60 CHARACTERS or Google truncates it
□ Most specific part FIRST, brand last
□ ⭐⭐ DYNAMIC ROUTES BUILD THE TITLE FROM THE DATA. A product page
   titled "Product" is a wasted result.
```

```tsx
// Next.js App Router
export const metadata = { title: "Pricing — Acme" };

export async function generateMetadata({ params }) {
  const p = await getProduct(params.id);
  return { title: `${p.name} — Acme` };      // ⭐ from the data
}
```

---

# 2 · ⭐ Meta descriptions

```
⭐⭐ IT DOES NOT AFFECT RANKING. IT DECIDES WHETHER ANYONE CLICKS.
   ⭐ It is the two lines under your link in the search result. If you
     do not write it, Google picks a random sentence from the page.

□ ⭐ 140–160 CHARACTERS. Longer is cut off.
□ UNIQUE PER PAGE — ⭐⭐ one description reused everywhere is worse
   than none, because every result looks identical
□ ⭐ WRITE IT AS A SENTENCE THAT MAKES SOMEONE CLICK, not a keyword list
□ Include what the page actually offers, and a reason to come

  ❌ "Acme - the best solution for your business needs. Acme, business,
      solutions, software, platform."
  ✅ "Compare Acme's three plans. Free for one project, £12/month for
      unlimited, no card required to start."
```

---

# 3 · ⭐⭐ Favicons

```
⭐⭐ THE FRAMEWORK DEFAULT LOGO ON A REAL PRODUCT IS A TELL. It says
   nobody finished.

THE FULL SET — not just favicon.ico:
  □ favicon.ico            32×32   ⭐ still needed, old browsers/bookmarks
  □ icon.svg                       ⭐ scales, tiny, supports dark mode
  □ apple-touch-icon.png  180×180  ⭐⭐ THE HOME-SCREEN ICON ON iOS.
                                     Missing it = a blurry screenshot.
  □ icon-192.png / icon-512.png    for the manifest
  □ site.webmanifest      name, short_name, theme_color, icons

⭐ GENERATE ALL OF THEM FROM ONE SVG — realfavicongenerator.net, or
  Next's app/icon.svg convention.
□ ⭐ CHECK IT ACTUALLY APPEARS. Hard-refresh — favicons cache
   aggressively and you will keep seeing the old one.
□ theme-color meta so the mobile browser chrome matches
```

---

# 4 · ⭐⭐ Open Graph — what a shared link looks like

```
⭐⭐ THIS IS WHAT APPEARS EVERY TIME ANYONE PASTES YOUR LINK INTO
   WHATSAPP, SLACK, IMESSAGE, LINKEDIN OR X.
   ⭐ WITHOUT IT: a grey box and a raw URL. It looks broken and nobody
     clicks it.
   ⇒ ⭐⭐ FOR A PRODUCT PEOPLE SHARE, THIS MATTERS MORE THAN SEO.
```

```html
<meta property="og:title"       content="Acme — ships in one click">
<meta property="og:description" content="One clear sentence.">
<meta property="og:image"       content="https://acme.com/og.png">
<meta property="og:url"         content="https://acme.com/pricing">
<meta property="og:type"        content="website">
<meta name="twitter:card"       content="summary_large_image">
```

```
□ ⭐ THE IMAGE IS 1200×630, UNDER 1MB, WITH AN ABSOLUTE URL
   ⭐⭐ A RELATIVE URL SILENTLY FAILS EVERYWHERE.
□ ⭐ TEXT ON THE IMAGE MUST BE READABLE AT THUMBNAIL SIZE
□ Dynamic pages get dynamic OG images if it is worth it
□ ⭐⭐ TEST BY PASTING THE REAL URL INTO WHATSAPP AND SLACK.
   Not a validator — the real thing, on the real domain.
   ⭐ Note these cache hard; use the platform's debugger to refresh.
```

---

# 5 · Crawlability

```
□ ⭐⭐ robots.txt EXISTS AND DOES **NOT** BLOCK THE SITE.
   ⭐ A staging `noindex` or `Disallow: /` shipped to production is a
     SILENT CATASTROPHE: the site simply never appears in Google, and
     nothing looks wrong.
   ⇒ ⭐⭐ CHECK THIS ON THE LIVE DOMAIN AFTER EVERY DEPLOY.
       curl https://yoursite.com/robots.txt
       grep -r "noindex" .next/ dist/

□ sitemap.xml, generated, submitted to Google Search Console
□ ⭐ CANONICAL URL on every page — prevents duplicate-content splits
   from ?utm_ params and trailing-slash variants
□ ⭐ ONE <h1> PER PAGE. Headings in order, no skipped levels.
□ lang attribute on <html>
□ ⭐⭐ CONTENT RENDERS WITHOUT JAVASCRIPT if it needs to be indexed.
   ⭐ Google runs JS, but slowly and unreliably. Server-render anything
     that must be found.
□ Structured data (JSON-LD) for products, articles, local businesses,
   FAQs — it is what produces rich results
□ Google Search Console verified, sitemap submitted, and ⭐ CHECK IT
   TWO WEEKS AFTER LAUNCH for coverage errors
```

---

# 6 · ⭐⭐ Broken links

```
⭐⭐ CRAWL THE BUILT SITE AND PROVE IT. Do not trust that they work.

   npx linkinator https://yoursite.com --recurse --skip "linkedin.com"

□ INTERNAL links all resolve
□ ⭐ EXTERNAL links all resolve — someone else's 404 still makes YOUR
   page look broken
□ ⭐⭐ NO LINKS TO PAGES YOU HAVE NOT BUILT YET. A footer full of dead
   "Careers" and "Blog" links is the clearest sign of an unfinished site.
   ⇒ ⭐ REMOVE THEM. An absent link beats a broken one.
□ No `href="#"` on anything that looks clickable
□ Anchor links land correctly — ⭐ `scroll-padding-top` if the header
   is fixed, or the heading hides behind it
□ ⭐ EXTERNAL LINKS: target="_blank" rel="noopener noreferrer"
   ⭐⭐ Without noopener the opened page can redirect YOUR tab to a
     phishing page.
□ Redirects for any URL you changed — ⭐ 301, not 302, for permanent moves
```

---

# 7 · ⭐ Contact links

```
⭐⭐ A PHONE NUMBER OR EMAIL AS PLAIN TEXT MAKES A MOBILE USER SELECT,
   COPY AND SWITCH APPS. MOST WILL NOT BOTHER.

□ EMAIL:
    <a href="mailto:hello@acme.com">hello@acme.com</a>
  ⭐ Optionally: ?subject=Enquiry%20from%20the%20website

□ ⭐⭐ PHONE — THE href IS +COUNTRYCODE AND DIGITS ONLY.
    ✅ <a href="tel:+919876543210">+91 98765 43210</a>
    ❌ <a href="tel:+91 98765 43210">     ⭐ SPACES BREAK IT
    ❌ <a href="tel:098765 43210">        ⭐ no country code = fails
                                            for anyone abroad
  ⭐ The DISPLAY text can be formatted however you like. The href cannot.

□ ⭐ ADDRESS LINKS TO A MAP
□ ⭐⭐ EVERY CONTACT METHOD YOU PUBLISH IS ONE YOU MUST MONITOR. An
   unread inbox on the contact page is worse than no contact page.
□ WhatsApp: https://wa.me/919876543210  (⭐ no +, no spaces)
```

---

# 8 · Analytics

```
□ ⭐ TRACK THE ONE ACTION THE SITE EXISTS FOR — signup, purchase,
   booking, enquiry. ⭐⭐ Page views tell you nothing you can act on.
□ Three events beat thirty: STARTED · COMPLETED · FAILED (with a reason)
   ⇒ ⭐ that gives you a FUNNEL and a FAILURE RATE, which are the only
     two numbers a site this size needs
□ ⭐⭐ NO PII IN EVENT PROPERTIES. Ids, never names or emails.
□ Respect Do Not Track and your own cookie notice
□ ⭐ SHIP THE web-vitals BEACON TOO (→ [07-Performance.md](07-Performance.md)) —
   real users, real devices, ~1KB
```

---

# The 2-minute check

```
① ⭐ LOOK AT THE BROWSER TAB — does it say the page name?
② ⭐⭐ PASTE THE URL INTO WHATSAPP — is there a proper preview card?
③ ⭐ curl /robots.txt — does it block anything it should not?
④ ⭐ VIEW SOURCE — is there a meta description, and is it specific?
⑤ ⭐ CLICK THE EMAIL AND PHONE IN THE FOOTER — do they open apps?
⑥ ⭐⭐ RUN linkinator — any broken links?
⑦ ⭐ CHECK THE FAVICON on a hard refresh, and on an iOS home screen
```

---

**Back:** [folder index](README.md) · **Full audit:** [10-Ship-Checklist.md §5–6](10-Ship-Checklist.md)
