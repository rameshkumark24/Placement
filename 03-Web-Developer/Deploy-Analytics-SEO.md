# 🚀 Deploy, Analytics & SEO

Step-by-step for getting a site live and measurable.

---

## 1. Deploy to Vercel

1. Push the repo to GitHub
2. Import the project in Vercel
3. Configure build settings (Next.js is auto-detected)
4. **Set environment variables** — production and preview separately
5. Deploy, and note the deployment URL
6. Verify preview deploys are created on pull requests

**Before the first production deploy**

- [ ] `.env` is gitignored and no secret is in the repo
- [ ] No secret is in a `NEXT_PUBLIC_*` variable
- [ ] Production and development use **different** database and API keys
- [ ] A spend cap is set on the Vercel project

---

## 2. Custom domain & DNS

1. Purchase the domain (Namecheap)
2. Add the domain in Vercel → Settings → Domains
3. Configure DNS at the registrar:

| Record | Name/Host | Value | TTL |
|---|---|---|---|
| `A` | `@` | Vercel's IP target | Auto |
| `CNAME` | `www` | `cname.vercel-dns.com` | Auto |

4. Decide www vs non-www as canonical and redirect the other
5. Wait for SSL to provision, then verify HTTPS works and HTTP redirects to it

---

## 3. Google Search Console

**Add the property**

1. Go to [Google Search Console](https://search.google.com/search-console)
2. Add a new property (Domain type, for full coverage)
3. Choose DNS verification

**DNS verification**

1. Copy the verification code from Google
2. At your registrar (Namecheap), add a TXT record:
   - **Type:** `TXT`
   - **Name/Host:** `@`
   - **Value:** the Google verification code
   - **TTL:** default
3. Click Verify in Search Console (DNS can take up to an hour to propagate)

**After verifying**

- [ ] Submit `sitemap.xml`
- [ ] Check Coverage for indexing errors
- [ ] Check Core Web Vitals report after ~28 days of data

---

## 4. Google Analytics (GA4)

**Create the property**

1. Go to [Google Analytics](https://analytics.google.com)
2. Create a new property:
   - Property name: your site name
   - Country: India
   - Currency: INR
   - Domain: your website URL

**Install the tracking code**

Copy your Measurement ID (format `G-XXXXXXXXXX`).

Raw HTML (`<head>`):

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

Next.js App Router — use the official component instead, it loads correctly with the router:

```tsx
// app/layout.tsx
import { GoogleAnalytics } from '@next/third-parties/google';

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
      <GoogleAnalytics gaId="G-XXXXXXXXXX" />
    </html>
  );
}
```

Then redeploy and confirm events arrive in GA4 Realtime.

> **Consent:** if you serve EU users (GDPR) or handle personal data under India's DPDP Act 2023,
> analytics needs a cookie consent banner and the tag should not fire before consent.

**Consider PostHog instead** if you care about funnels, retention and session replay rather than
raw traffic counts — it answers "where do users drop off", which GA4 makes hard.

---

## 5. SEO checklist

### Meta tags
- [ ] Unique page title per page (50–60 chars)
- [ ] Meta description per page (150–160 chars)
- [ ] Open Graph tags + OG image (1200×630) for social sharing
- [ ] Twitter card tags
- [ ] Favicon (and apple-touch-icon)
- [ ] Canonical URL on every page

### Content
- [ ] Alt text on all images
- [ ] One `<h1>` per page, headings in hierarchical order
- [ ] Internal linking between related pages
- [ ] Mobile-friendly (Google indexes mobile-first)

### Technical
- [ ] `sitemap.xml` generated and submitted
- [ ] `robots.txt` correct — and **not blocking production**
- [ ] Schema markup / structured data (Organization, Product, FAQ, Article)
- [ ] SSL certificate active, HTTP → HTTPS redirect
- [ ] No duplicate content between www and non-www

### Performance (a ranking factor)
- [ ] Images compressed, WebP/AVIF
- [ ] Lazy loading below the fold
- [ ] Redirect chains minimised
- [ ] Fast server response (TTFB < 600ms)

---

## 6. Testing tools

### Google Lighthouse (built into Chrome)
1. Open the site
2. `F12` → **Lighthouse** tab
3. Run the audit for Performance, Accessibility, Best Practices, SEO
4. Target ≥ 90 on all four

### PageSpeed Insights
1. Visit [pagespeed.web.dev](https://pagespeed.web.dev)
2. Enter the URL
3. Check **both** Mobile and Desktop — mobile is always worse and is what Google ranks on
4. Work the Opportunities list top-down

### Others worth running once
| Tool | Checks |
|---|---|
| [securityheaders.com](https://securityheaders.com) | Security header grade |
| [ssllabs.com/ssltest](https://www.ssllabs.com/ssltest/) | TLS configuration |
| [wave.webaim.org](https://wave.webaim.org) | Accessibility issues |
| [mail-tester.com](https://www.mail-tester.com) | Email deliverability / spam score |

---

## 7. Post-deploy smoke test

Run every time you deploy to production:

- [ ] Home page loads over HTTPS
- [ ] Signup → email received → verify → login works
- [ ] Password reset email arrives (check spam)
- [ ] The core feature works end to end
- [ ] A payment completes in live mode (small real charge, then refund)
- [ ] 404 page renders for a bad URL
- [ ] Mobile layout correct on a real phone
- [ ] Sentry received a test error
- [ ] Analytics shows your own visit in Realtime

---

## 8. Optional but usually wanted

- [ ] Dark mode toggle
- [ ] Cookie consent banner
- [ ] Custom 404 page
- [ ] WhatsApp floating button (high conversion for Indian audiences)
- [ ] Live chat / chatbot
- [ ] Newsletter capture + automated welcome email
- [ ] Blog / news section for SEO
- [ ] Social sharing buttons

---

## Quick reference

| Resource | URL | Purpose |
|---|---|---|
| shadcn/ui | https://ui.shadcn.com/docs/components | Dashboard components |
| ReactBits | https://reactbits.dev/get-started/index | Animations |
| HeroUI | https://www.heroui.com/docs/guide/introduction | Hero sections |
| Google Search Console | https://search.google.com/search-console | SEO monitoring |
| Google Analytics | https://analytics.google.com | Traffic analysis |
| PageSpeed Insights | https://pagespeed.web.dev | Performance testing |
| Namecheap | https://www.namecheap.com | Domain registration |
| Vercel | https://vercel.com | Hosting + preview deploys |
| Sentry | https://sentry.io | Error tracking |
| Resend | https://resend.com | Transactional email |
