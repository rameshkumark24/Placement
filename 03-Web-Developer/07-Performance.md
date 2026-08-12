# Phase 07 — Performance

Targets: **LCP < 2.5s · INP < 200ms · CLS < 0.1 · Lighthouse ≥ 90**

**Gate to pass this phase:** Lighthouse ≥ 90 on all four categories, tested against a realistically
sized dataset — not 20 demo rows.

---

## 1. Database — where it actually breaks first

Most "the site is slow" problems are one missing index or one N+1 query.

- [ ] **Indexes on every foreign key** and every column used in `WHERE` / `ORDER BY`
- [ ] Composite index matching your actual list query (`(user_id, created_at desc)`)
- [ ] `EXPLAIN ANALYZE` run on the five slowest queries
- [ ] **N+1 queries eliminated** — the single most common AI-generated performance bug
- [ ] Pagination on every list endpoint, with a **server-enforced** maximum
- [ ] Cursor pagination instead of `OFFSET` for large tables — `OFFSET 10000` scans 10,000 rows
- [ ] Named columns, never `select('*')`
- [ ] **Connection pooling on** (Supabase port 6543) — mandatory with serverless
- [ ] **Tested against 100k+ seeded rows**, not 20 demo rows
- [ ] Archival plan for tables that grow forever (logs, events, audit)

```ts
// 💀 N+1 — 1 query for posts, then 1 per post for the author
const posts = await supabase.from('posts').select('*');
for (const p of posts.data!) {
  const author = await supabase.from('users').select('name').eq('id', p.user_id).single();
}

// ✅ One query
const posts = await supabase
  .from('posts')
  .select('id, title, created_at, users(name)')     // nested select = join
  .eq('user_id', userId)
  .order('created_at', { ascending: false })
  .limit(50);
```

**Seed realistic data before you trust any performance number.** An app that's fast at 20 rows tells
you nothing.

---

## 2. Next.js specifics

- [ ] **Server Components by default**; `'use client'` only where interaction requires it
- [ ] `'use client'` pushed as far down the tree as possible — it makes everything below it client-side
- [ ] `next/image` for every image, never a raw `<img>`
- [ ] `next/font` for fonts — prevents layout shift and a render-blocking request
- [ ] Explicit `width`/`height` on media so nothing shifts as it loads
- [ ] Heavy client components dynamically imported (`next/dynamic`)
- [ ] Bundle analysed (`@next/bundle-analyzer`) — check what's actually in it
- [ ] Route-level code splitting
- [ ] Streaming + `<Suspense>` for slow sections, so the shell paints immediately
- [ ] `revalidate` / `cache` set deliberately on every fetch — Next.js caching defaults surprise people
- [ ] Third-party scripts via `next/script` with the right strategy

---

## 3. Caching

| Layer | Use for |
|---|---|
| Cloudflare CDN | Static assets, images, public pages |
| Next.js Data Cache | Fetches that don't change per user |
| Upstash Redis | Hot reads, expensive third-party responses, computed aggregates |
| TanStack Query | Client-side dedupe + stale-while-revalidate |

- [ ] Cache headers on static assets (long max-age + immutable, with hashed filenames)
- [ ] Expensive third-party calls cached by input hash
- [ ] Cache invalidation decided deliberately — a stale cache is a bug that looks like a data bug
- [ ] **Never cache a per-user response in a shared cache** — that's a cross-user data leak

---

## 4. Frontend

- [ ] Images: WebP/AVIF, correctly sized, lazy-loaded below the fold
- [ ] Compression (gzip/brotli) enabled
- [ ] Skeletons / optimistic UI so waits feel shorter
- [ ] Search and autocomplete debounced ([Phase 05](05-API-Safety.md#5-the-keystroke-storm))
- [ ] Long lists virtualised
- [ ] Unused CSS/JS removed
- [ ] No render-blocking third-party scripts above the fold

---

## 5. Core Web Vitals — what each one actually means

| Metric | Target | Usual cause when it's bad |
|---|---|---|
| **LCP** | < 2.5s | Unoptimised hero image, slow server response, render-blocking font |
| **INP** | < 200ms | Heavy JS on the main thread, unmemoised re-renders |
| **CLS** | < 0.1 | Images without dimensions, fonts swapping, injected banners |

Measure with real data (Vercel Speed Insights / Search Console), not just lab data. Lab Lighthouse
runs on a fast machine and flatters you.

---

## 6. Load testing

- [ ] Load test at **10× expected peak** before launch (k6 or Artillery)
- [ ] Test the real user journey, not just the home page
- [ ] Watch: p95 latency, error rate, DB connection count, Vercel function duration
- [ ] **Know where it breaks first, and what the fix is** — that's the actual deliverable of this step

```js
// k6 — ramp to 10x peak
import http from 'k6/http';
import { check } from 'k6';

export const options = {
  stages: [
    { duration: '1m', target: 50 },
    { duration: '3m', target: 500 },
    { duration: '1m', target: 0 },
  ],
  thresholds: { http_req_duration: ['p(95)<1000'], http_req_failed: ['rate<0.01'] },
};

export default function () {
  const res = http.get(`${__ENV.BASE_URL}/api/items?limit=20`);
  check(res, { 'status 200': r => r.status === 200 });
}
```

> Load-test against **staging**, never production, and warn Upstash/Supabase-sized free tiers first —
> you can exhaust a month's quota in three minutes.

---

## Phase gate

- [ ] Lighthouse ≥ 90 on Performance, Accessibility, Best Practices, SEO
- [ ] Tested at 100k+ rows
- [ ] No N+1 in the critical path
- [ ] Pooler connection in use
- [ ] Load test run; the first breaking point is known
