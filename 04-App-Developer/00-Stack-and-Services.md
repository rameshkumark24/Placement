# Phase 00 — Stack & Services

**Gate to pass this phase:** store accounts registered (they take days to approve), every service
account created, every spend cap set.

---

## Setup order

```
1. Store accounts   Apple + Google — REGISTER FIRST, approval takes days to weeks
2. GitHub           repo + branch protection + secret scanning
3. Expo / EAS       project, build profiles, EAS Update channel
4. Supabase         database + RLS + storage
5. Clerk            auth, Expo SDK, biometric unlock
6. Upstash          rate limiting — before any public endpoint ships
7. Sentry           JS errors + native crash symbolication
8. Cloudflare       API edge protection, R2 for assets
9. Stripe / IAP     payments — decide which one applies before building
10. Pinecone        only if the app has semantic search / RAG
```

---

## Store accounts — do this first

These gate your launch date and cannot be rushed.

| | Apple | Google |
|---|---|---|
| Cost | ₹8,900 / year | $25 one-time |
| Approval | Up to a week (longer for orgs) | Usually 48h |
| Org accounts need | **D-U-N-S number — can take 2 weeks** | Business verification |
| Review per release | 1–3 days | Hours to 2 days |

> **New personal Google Play accounts must run a closed test with 12+ testers for 14 days before
> publishing publicly.** This surprises people and delays launches by two weeks. Start it early.

- [ ] Both accounts registered and approved
- [ ] App name availability checked on **both** stores
- [ ] Bundle ID / package name decided — **it cannot be changed after publishing**

---

## React Native + Expo

**Why Expo over bare RN:** EAS Build lets you ship an iOS app without a Mac, and EAS Update lets
you push a JS-only fix without a store review. That second point is worth more than any other
tooling decision here.

- [ ] Expo SDK 52+ with the New Architecture
- [ ] **EAS Update configured on day one** — it's your kill switch, not a nice-to-have
- [ ] Build profiles: `development`, `preview`, `production` with **separate env vars per profile**
- [ ] `app.config.ts` (not static `app.json`) so config can vary by environment
- [ ] Minimum OS versions chosen — iOS 15+ / Android 8+ is a sane 2026 default
- [ ] EAS Build credentials managed by Expo (safer than local keystores you can lose)

**When Flutter instead:** heavy custom animation, or you want one rendering engine guaranteeing
pixel-identical output on both platforms.

### Core libraries

| Need | Library |
|---|---|
| Navigation | `expo-router` |
| Styling | NativeWind (Tailwind for RN) |
| Components | React Native Reusables |
| Server state | `@tanstack/react-query` |
| Local state | `zustand` |
| Secure storage | `expo-secure-store` |
| Fast key-value | `react-native-mmkv` |
| Local DB | `expo-sqlite` |
| Images | `expo-image` |
| Long lists | `@shopify/flash-list` |
| Forms | `react-hook-form` + `zod` |
| Network status | `@react-native-community/netinfo` |
| Animation | `react-native-reanimated` |
| Push | `expo-notifications` |

---

## GitHub

- [ ] `.gitignore` covers `.env*`, `ios/`, `android/`, `node_modules` — **before the first commit**
- [ ] Branch protection on `main`: PR required, CI required, no force-push
- [ ] **Secret scanning + push protection on** — blocks a commit containing a key before it reaches
      the remote
- [ ] Dependabot alerts + security updates
- [ ] Actions CI: typecheck, lint, test on every PR
- [ ] **Signing keys backed up** — lose the Android keystore and you can never update the app.
      Use Play App Signing.

---

## Supabase

- [ ] Separate projects for dev and prod
- [ ] **RLS on every table** — the anon key ships inside your app bundle, where anyone can read it
- [ ] Policies tested with a second real account
- [ ] `service_role` key **never** in the app — server-side only
- [ ] Connection pooling on (port 6543)
- [ ] Daily backups on, restore tested once
- [ ] Storage buckets with their own policies

> On mobile this matters more than on web: your anon key isn't just in a bundle a user could
> inspect — it's in a file distributed to every device, permanently. RLS is the only thing
> standing between it and your data.

---

## Clerk

- [ ] Expo SDK installed, token cache backed by **`expo-secure-store`** (not AsyncStorage)
- [ ] Session lifetime short; refresh rotation on
- [ ] **Biometric unlock** (Face ID / fingerprint) for re-entry on anything valuable
- [ ] MFA available
- [ ] Webhooks signature-verified, syncing to your own `users` table
- [ ] Roles enforced server-side on every request
- [ ] Account deletion flow **in the app** — both stores require it

---

## Upstash

Rate limiting matters more on mobile than web: **you cannot patch a buggy client quickly.** A loop
bug in a released version keeps hammering you until users update — which some never will.

- [ ] Rate limits on every write, auth and expensive endpoint before public release
- [ ] Caching for hot reads
- [ ] Idempotency key storage
- [ ] Separate dev and prod databases
- [ ] **Budget alert set** — per-request billing means a client loop bills you

Suggested: auth `5/min` · writes `30/min` · reads `100/min` · AI `10/min`.

---

## Sentry

- [ ] RN SDK installed, capturing **JS errors and native crashes**
- [ ] **dSYMs (iOS) and ProGuard mappings (Android) uploaded** — without them a native crash is
      unreadable hex
- [ ] Source maps uploaded for the JS bundle
- [ ] Release + dist tagged, matching your build numbers
- [ ] **PII scrubbing on**; user ID only, never email
- [ ] `tracesSampleRate` set deliberately
- [ ] Alerts routed somewhere you'll see at 2am
- [ ] Crash-free session rate as your headline metric

---

## Payments — decide this before building

**This decision is architectural, not cosmetic.**

| Selling | iOS | Android |
|---|---|---|
| Physical goods / real-world services | Stripe / Razorpay — **exempt from IAP** | Any payment SDK |
| Digital goods, subscriptions, in-app content | **Apple IAP mandatory** (15–30% fee) | Google Play Billing |

- [ ] Which category you're in, decided and written down
- [ ] If IAP: `expo-in-app-purchases` or RevenueCat, receipt validation **server-side**
- [ ] If Stripe: React Native SDK (native payment sheet, **not** a WebView)
- [ ] Idempotency keys on every charge
- [ ] Webhook signature verified, handler idempotent
- [ ] Amounts computed server-side, never trusted from the client
- [ ] Restore-purchases flow implemented (Apple requires it)

> Getting this wrong is a guaranteed rejection under Apple guideline 3.1.1, discovered after you've
> built the whole payment flow.

---

## Cloudflare

Your API sits behind it even though the client is an app.

- [ ] WAF managed rules on
- [ ] Rate limiting on auth endpoints (defence in depth with Upstash)
- [ ] **Block `/.env`, `/.git/*`** on any API domain you control ([Phase 06](06-Security.md))
- [ ] R2 for user-uploaded media — no egress fees, which matters when phones download images
- [ ] Cache rules for static assets and images

---

## Pinecone

*Only if the app has semantic search or RAG. `pgvector` in Supabase is free and usually enough.*

- [ ] **Namespace or metadata filter per user/tenant** — the RAG equivalent of RLS
- [ ] Retrieval filtered by the requesting user **in the query**
- [ ] Top-k bounded, token budget set
- [ ] All calls proxied through your backend — **never** put the Pinecone key in the app

---

## Cost model

Estimate at **10 / 1,000 / 10,000 users** now.

| Service | Watch |
|---|---|
| EAS Build | Build minutes — each production build costs |
| Supabase | DB size, egress, connections |
| Clerk | Monthly active users |
| Upstash | **Per-request billing — a client loop bills you and you can't patch fast** |
| Sentry | Event quota; a crash loop burns it in an hour |
| Cloudflare R2 | Storage (no egress fees) |
| Apple / Google | 15–30% on digital goods |

**Set a hard ceiling and alerts at 50% and 80% on every one.**

---

## Environment variables

| Variable | Where |
|---|---|
| `EXPO_PUBLIC_SUPABASE_URL` | In the bundle — fine |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | In the bundle — fine **only if RLS is on every table** |
| `EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY` | In the bundle — fine |
| `SUPABASE_SERVICE_ROLE_KEY` | **Server only. Never in the app.** |
| `CLERK_SECRET_KEY` | Server only |
| `STRIPE_SECRET_KEY` | Server only |
| `UPSTASH_REDIS_REST_TOKEN` | Server only |
| `PINECONE_API_KEY` | Server only |

> **`EXPO_PUBLIC_*` variables are compiled into the binary.** They are readable by anyone who
> downloads your app. There is no such thing as a secret on a device.

Verify before every release:

```bash
npx expo export
grep -rE "service_role|sk_live|CLERK_SECRET|PINECONE_API" dist/ && echo "🚨 LEAK" || echo "✅ clean"
```
