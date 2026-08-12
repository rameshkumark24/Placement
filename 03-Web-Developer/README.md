# 03 — Web Developer

| File | Contents |
|---|---|
| [Web-Development-Cheatsheet.md](Web-Development-Cheatsheet.md) | **Main guide.** Stack, build order, page inventory, security, API safety, performance, a11y, launch gate |
| [UI-Component-Libraries.md](UI-Component-Libraries.md) | shadcn/ui, ReactBits, HeroUI, fonts, icons, effects catalogue |
| [Deploy-Analytics-SEO.md](Deploy-Analytics-SEO.md) | Vercel deploy, domain/DNS, Search Console, GA4, Lighthouse, SEO checklist |
| [Reference/API-Notes.md](Reference/API-Notes.md) | Saved links |

> Universal rules live in [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/) and are not repeated here.

## Default stack

Next.js 15 (App Router) · TypeScript · Tailwind + shadcn/ui · Supabase (Postgres + Auth) ·
TanStack Query · Vercel · Sentry

## The three that cause the most damage

1. **`useEffect` without a correct dependency array** → millions of requests, huge bill.
   → [API safety](Web-Development-Cheatsheet.md#6-web-specific-api-safety)
2. **A secret in a `NEXT_PUBLIC_*` var** → compiled into the browser bundle, public forever.
   → [Security](Web-Development-Cheatsheet.md#5-web-specific-security)
3. **No ownership filter in the query** → any user reads any row.
   → [The IDOR test](Web-Development-Cheatsheet.md#the-idor-test-do-this-manually-before-every-launch)
