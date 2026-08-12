# Phase 10 — Deploy & Launch

**Gate to pass this phase:** smoke test passed on production, exposure check clean, rollback command
ready in a terminal.

---

## 1. Environment separation

- [ ] Dev / Preview / Production fully separated — **separate databases, separate keys**
- [ ] Preview deploys use test keys and a non-production database
- [ ] Secrets in Vercel env vars per environment, never in the repo or CI logs
- [ ] Stripe in test mode everywhere except production
- [ ] Clerk test instance for dev/preview, live instance for production

> A preview deploy pointed at your production database is a public URL with real user data on it.

---

## 2. Deploy to Vercel

1. Push to GitHub
2. Import the project in Vercel (Next.js is auto-detected)
3. Set environment variables — **Production and Preview separately**
4. Deploy, note the URL
5. Verify preview deploys are created on pull requests

- [ ] Build fails on typecheck/lint errors — don't let it ship broken code
- [ ] Migrations run as part of deploy, reversibly
- [ ] Zero-downtime deploy verified
- [ ] Function timeouts and memory set deliberately
- [ ] **Spend cap set** with an alert below it
- [ ] Deployment protection on preview URLs if they touch real data

---

## 3. Domain & DNS (Cloudflare)

1. Buy the domain (Namecheap, or Cloudflare Registrar at cost)
2. Point nameservers at Cloudflare
3. Add the domain in Vercel → Settings → Domains
4. Add DNS records at Cloudflare:

| Record | Name | Value | Proxy |
|---|---|---|---|
| `A` | `@` | Vercel's IP target | Proxied |
| `CNAME` | `www` | `cname.vercel-dns.com` | Proxied |

5. Decide www vs non-www as canonical, redirect the other
6. Wait for SSL, then verify HTTPS works and HTTP redirects to it

### Cloudflare settings

- [ ] SSL/TLS mode **Full (strict)** — "Flexible" leaves traffic unencrypted behind the proxy
- [ ] Always Use HTTPS on
- [ ] HSTS enabled once you're confident (it's hard to undo)
- [ ] **WAF managed rules on**
- [ ] Bot Fight Mode on
- [ ] Rate limiting rules on `/api/auth/*`
- [ ] **Block rule for `/.env`, `/.git/*`, `/.aws`, `/.ssh`** ([Phase 06](06-Security.md#block-it-at-cloudflare-too-defence-in-depth))
- [ ] Caching rules for static assets

---

## 4. Email deliverability

Your password reset emails go to spam without this.

- [ ] SPF record published
- [ ] DKIM configured with your email provider (Resend)
- [ ] DMARC record published (start at `p=none`, tighten later)
- [ ] Sending domain verified with the provider
- [ ] Tested at [mail-tester.com](https://www.mail-tester.com) — aim for 9+/10
- [ ] Reset and verification emails checked in Gmail **and** Outlook, including the spam folder

---

## 5. SEO

### Meta
- [ ] Unique title per page (50–60 chars)
- [ ] Meta description per page (150–160 chars)
- [ ] Open Graph tags + OG image (1200×630)
- [ ] Twitter card tags
- [ ] Favicon + apple-touch-icon
- [ ] Canonical URL on every page

### Content
- [ ] Alt text on all images
- [ ] One `<h1>` per page, headings in order
- [ ] Internal linking
- [ ] Mobile-friendly (Google indexes mobile-first)

### Technical
- [ ] `sitemap.xml` generated and submitted
- [ ] `robots.txt` correct — and **not blocking production**
- [ ] Structured data (Organization, Product, FAQ, Article)
- [ ] SSL active, HTTP → HTTPS redirect
- [ ] No duplicate content between www and non-www

---

## 6. Google Search Console

**Add the property**
1. [Google Search Console](https://search.google.com/search-console) → add property (Domain type)
2. Choose DNS verification

**DNS verification**
1. Copy the verification code
2. At Cloudflare, add a TXT record: Name `@`, Value = the code, TTL auto
3. Click Verify (propagation can take an hour)

**After verifying**
- [ ] Submit `sitemap.xml`
- [ ] Check Coverage for indexing errors
- [ ] Check Core Web Vitals after ~28 days of data

---

## 7. Google Analytics (GA4)

1. [Google Analytics](https://analytics.google.com) → create property
   - Country: India · Currency: INR · Domain: your URL
2. Copy the Measurement ID (`G-XXXXXXXXXX`)

```tsx
// app/layout.tsx — the official component, loads correctly with the App Router
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

Raw HTML alternative (`<head>`):

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

- [ ] Redeploy and confirm events in GA4 Realtime
- [ ] **Cookie consent banner if serving EU users (GDPR) or handling personal data under
      India's DPDP Act 2023** — the tag must not fire before consent

---

## 8. Legal pages — required before public launch

- [ ] **Privacy Policy** published and linked in the footer
- [ ] **Terms & Conditions** published
- [ ] Cookie consent if using analytics or third-party cookies
- [ ] **DPDP Act 2023 (India)** — consent notice, purpose limitation, grievance officer contact if
      handling personal data at scale
- [ ] GDPR if any EU users — lawful basis, data export, right to erasure
- [ ] **Account deletion flow that genuinely deletes** (or documented anonymisation)
- [ ] Data export available to users
- [ ] Data retention policy written
- [ ] Refund policy if charging money
- [ ] Licence check on every dependency and template used

---

## 9. Reliability before launch

- [ ] Automated daily backups on
- [ ] **Restore actually performed once** into a scratch environment — an untested backup is not a backup
- [ ] Point-in-time recovery on if the data matters
- [ ] RTO and RPO written down
- [ ] Rollback procedure documented and rehearsed once
- [ ] Feature flags / kill switch for risky features
- [ ] Staging environment mirroring production
- [ ] Runbook: top five likely incidents, first three steps for each

---

## 10. Launch gate

- [ ] CI green: typecheck, lint, tests, build
- [ ] `/code-review` and `/security-review` clean ([Phase 08](08-Testing-and-Review.md))
- [ ] **Exposure check clean** — `/.env`, `/.git/config` return 404 ([Phase 06](06-Security.md#0-the-exposure-check--do-this-on-every-deploy))
- [ ] **IDOR test passed**
- [ ] Client bundle contains no secrets
- [ ] Custom domain + SSL live, redirect settled
- [ ] 404 and 500 pages designed
- [ ] Email deliverability verified
- [ ] **Payment tested in live mode with a real small charge, then refunded**
- [ ] Sentry receiving events with source maps
- [ ] Analytics firing
- [ ] Uptime monitor live
- [ ] Spend caps + billing alerts set
- [ ] Legal pages published and linked
- [ ] Backup restore tested
- [ ] **Rollback command ready in a terminal during launch**

---

## 11. Smoke test — run immediately after every production deploy

- [ ] Home page loads over HTTPS
- [ ] Signup → verification email arrives → verify → login
- [ ] Password reset email arrives (check spam)
- [ ] Core feature works end to end
- [ ] A payment completes in live mode, then refund it
- [ ] 404 page renders for a bad URL
- [ ] Mobile layout correct on a real phone
- [ ] Sentry received a deliberate test error
- [ ] Analytics shows your own visit in Realtime
- [ ] `curl -o /dev/null -w "%{http_code}" https://yourdomain.com/.env` → **404**

---

## 12. Soft launch

- [ ] Release to a small group first — friends, a waitlist segment, one community
- [ ] Watch error rate and latency for the first hours
- [ ] Fix what breaks before the public announcement
- [ ] Only then announce

---

## Quick reference

| Resource | URL |
|---|---|
| Vercel | https://vercel.com |
| Cloudflare | https://dash.cloudflare.com |
| Supabase | https://supabase.com/dashboard |
| Clerk | https://dashboard.clerk.com |
| Stripe | https://dashboard.stripe.com |
| Upstash | https://console.upstash.com |
| Sentry | https://sentry.io |
| Search Console | https://search.google.com/search-console |
| Google Analytics | https://analytics.google.com |
| PageSpeed Insights | https://pagespeed.web.dev |
| securityheaders.com | https://securityheaders.com |
| mail-tester.com | https://www.mail-tester.com |
| Namecheap | https://www.namecheap.com |
