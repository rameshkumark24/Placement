# Phase 06 — Security

**OWASP Top 10:2025** — #1 Broken Access Control, #2 Security Misconfiguration, #3 Software Supply
Chain Failures. Mobile adds one more principle that changes everything:

> **The client is fully under the attacker's control.** Anyone can unzip your APK, read every
> string, hook the runtime, and forge any request your app can make. Nothing enforced on the device
> is enforced at all.

**Gate to pass this phase:** no secret in the bundle, tokens in SecureStore, IDOR test passed,
`/.env` and `/.git/config` return 404 on your API domain.

---

## 0. The exposure check — on every API domain you control

Attackers scan every domain automatically, within hours of it going live. **Each hit is a total
compromise.**

```bash
DOMAIN=api.yourdomain.com

for path in \
  /.env /.env.local /.env.production /.env.backup \
  /.git/config /.git/HEAD /.git/logs/HEAD \
  /.svn/entries /.DS_Store \
  /config.json /credentials.json /backup.sql /dump.sql \
  /.aws/credentials /.ssh/id_rsa \
  /server-status /actuator/env /debug
do
  code=$(curl -s -o /dev/null -w "%{http_code}" "https://$DOMAIN$path")
  [ "$code" = "404" ] || [ "$code" = "403" ] \
    && echo "ok   $code  $path" \
    || echo "LEAK $code  $path"
done
```

**Every line must be 404 or 403.**

- **`/.env`** contains every key: `SUPABASE_SERVICE_ROLE_KEY` (bypasses all RLS), `CLERK_SECRET_KEY`
  (impersonate any user), `STRIPE_SECRET_KEY`, `UPSTASH_REDIS_REST_TOKEN`, `PINECONE_API_KEY`.
- **`/.git/config`** returning 200 means the whole `.git` directory is served. An attacker runs
  `git-dumper` and reconstructs **your entire repository — every commit, every branch, and every
  secret ever committed and later "removed"**. Deleting a key from current files never removed it
  from history.

Block at Cloudflare too (WAF → Custom rule → Block):

```
(http.request.uri.path contains "/.env")
or (http.request.uri.path contains "/.git")
or (http.request.uri.path contains "/.aws")
or (http.request.uri.path contains "/.ssh")
```

**If any returned 200:** rotate every key immediately, fix the serving config, re-check, then audit
Supabase/Stripe/Clerk logs for unauthorised activity.

---

## 1. The bundle is public

```bash
# What an attacker does in 30 seconds
unzip -o app-release.apk -d extracted/
grep -rE "sk_live|service_role|secret|password|api[_-]?key" extracted/
```

- [ ] **No secret key in the app bundle** — not in code, not in `.env`, not in `app.config.ts`
- [ ] **`EXPO_PUBLIC_*` variables are compiled into the binary and fully readable.** Only genuinely
      public values belong there.
- [ ] Anything requiring a secret goes through **your backend**, which holds the key
- [ ] Third-party keys that must ship (Maps, analytics) are **restricted by bundle ID / SHA
      fingerprint** in that vendor's console — that restriction is what makes shipping them safe
- [ ] Verified before every release:

```bash
npx expo export
grep -rE "service_role|sk_live|CLERK_SECRET|PINECONE_API|UPSTASH_.*TOKEN" dist/ \
  && echo "🚨 LEAK" || echo "✅ clean"
```

---

## 2. Token storage

```ts
// 💀 Plain text on a rooted/jailbroken device
await AsyncStorage.setItem('token', jwt);

// ✅ Keychain (iOS) / Keystore (Android), hardware-backed
import * as SecureStore from 'expo-secure-store';
await SecureStore.setItemAsync('token', jwt);
```

- [ ] Tokens in **`expo-secure-store`** — never AsyncStorage, MMKV-without-encryption, or
      SharedPreferences
- [ ] Clerk's Expo token cache configured to use SecureStore
- [ ] Short access-token expiry, refresh rotation on
- [ ] Tokens cleared on logout, including from memory
- [ ] **Biometric gate on re-entry** for anything financial or sensitive
- [ ] Session revocable server-side — logout must invalidate, not just clear locally

---

## 3. Authorization — the IDOR layer

A mobile client is fully attacker-controlled, so **every** authorization decision must be
server-side. There is no exception.

```ts
// 💀 Any user reads any note
const { data } = await supabase.from('notes').select('*').eq('id', noteId).single();

// ✅ Ownership enforced in the query
const { data } = await supabase
  .from('notes')
  .select('id, title, body')
  .eq('id', noteId)
  .eq('user_id', userId)          // ← the line that matters
  .single();
if (!data) return notFound();
```

- [ ] Every query filters by the authenticated user's ID, server-side
- [ ] **RLS on every table**, tested with a second account ([Phase 03](03-Architecture-and-Data.md#2-rls--mandatory-here))
- [ ] Mass assignment blocked — whitelist updatable fields
- [ ] Admin functionality behind a server-side guard, not a hidden screen
- [ ] Return **404, not 403**, for resources the user doesn't own
- [ ] No authorization logic in the app that isn't also enforced on the server

### The IDOR test — before every release

1. Create accounts A and B.
2. As A, create a resource. Capture its ID (proxy the app through Burp/Charles, or read your logs).
3. As B, request A's resource ID directly.
4. **Expect 403/404.**
5. Repeat for update and delete.
6. Repeat with the auth header stripped entirely.

---

## 4. Transport

- [ ] HTTPS only; **cleartext traffic disabled** (`usesCleartextTraffic=false` on Android, ATS
      enabled on iOS)
- [ ] **Certificate pinning** if the app handles money or health data — it stops an attacker with a
      proxy on the same network reading traffic. Plan a rotation path, or a cert change bricks your app.
- [ ] Cloudflare SSL/TLS mode Full (strict)
- [ ] No debug proxy configuration left in release builds

---

## 5. Platform hardening

- [ ] **Code obfuscation on release builds** (ProGuard/R8 on Android, Hermes bytecode)
- [ ] Root/jailbreak detection for high-value apps — a speed bump, not a wall
- [ ] **Deep link parameters validated** — a malicious link must not navigate into an authenticated
      screen or trigger an action. Treat every deep link as untrusted input.
- [ ] WebViews: `javaScriptEnabled` only if required, URL allowlist enforced, never load
      user-supplied URLs
- [ ] **`FLAG_SECURE` on sensitive screens** — blocks screenshots and hides content in the app
      switcher (payment screens, tokens, OTPs)
- [ ] Clipboard never used for OTPs or tokens
- [ ] No sensitive data in release-build logs
- [ ] Exported Android activities/receivers audited — an exported activity is an entry point
- [ ] Backup rules exclude sensitive data (`android:allowBackup="false"` or explicit rules)

---

## 6. Permissions

- [ ] Only permissions you actually use — unused ones cause rejections **and** widen your attack surface
- [ ] Purpose strings specific and honest (vague ones get rejected)
- [ ] Priming screen before every OS dialog
- [ ] Graceful denied path for each
- [ ] Background location avoided unless essential — it triggers heavy store scrutiny

---

## 7. Input & endpoints

- [ ] Zod validation at every API boundary, server-side
- [ ] Parameterised queries only
- [ ] File uploads: type allowlist, size cap, sanitised filename, separate origin
- [ ] Rate limiting per user and per IP ([Phase 05](05-API-Safety.md#7-rate-limiting-with-upstash))
- [ ] Webhook signatures verified (Stripe, Clerk, RevenueCat)
- [ ] **IAP receipts validated server-side** — a client-side "purchase succeeded" is trivially forged
- [ ] Production errors carry no stack traces or DB details
- [ ] Debug endpoints and seed routes removed
- [ ] SSRF: user-supplied URLs validated against an allowlist

---

## 8. Supply chain

- [ ] Every package verified: exists, maintained, real downloads, real repo
- [ ] Package names read character by character (slopsquatting)
- [ ] **Native modules audited** — they run with full app privileges, outside the JS sandbox
- [ ] Lock file committed
- [ ] `pnpm audit` + Dependabot in CI
- [ ] Licence check on every dependency
- [ ] Every dependency ships to every user's device — minimise them

---

## 9. AI features

- [ ] **All model calls proxied through your backend** — never a provider key in the app
- [ ] Per-user quotas and a hard spend cap
- [ ] Model output never executed; escaped before display
- [ ] Prompt injection handled — user content delimited
- [ ] **Pinecone/RAG retrieval filtered by the requesting user's ID in the query**
- [ ] PII not sent to third-party models without consent and a DPA

---

## 10. Privacy — which is also a store requirement

- [ ] Apple **privacy nutrition label** accurate, including analytics and crash-reporting SDKs
- [ ] Google **Data safety form** accurate and consistent with actual SDK behaviour
- [ ] Declarations match reality — mismatches are found and rejected
- [ ] **In-app account deletion** — required by both stores
- [ ] Data export available
- [ ] PII never logged or sent to Sentry
- [ ] DPDP Act 2023 (India) consent notice; GDPR handling if EU users
- [ ] Third-party SDK data sharing disclosed

---

## AI-generated code review gate

| Check | Why agents fail it |
|---|---|
| Auth check present | Forgotten on "internal" routes |
| Ownership check present | The IDOR default |
| Input validated server-side | Validated in the app and called done |
| Token storage uses SecureStore | AsyncStorage is the default the agent reaches for |
| No secret in an `EXPO_PUBLIC_` var | Agents don't distinguish public from secret |
| Listeners cleaned up | Cleanup returns omitted |
| Rate limit applied | Never added unless told |

Automate it: **`/security-review`** — see [Phase 08](08-Testing-and-Review.md).

---

## Pre-release security test

1. **Exposure check** — `/.env`, `/.git/config` return 404 on the API domain
2. **Bundle check** — `grep` the export for secrets, returns nothing
3. **IDOR test** — user B blocked from user A's data, on read, update and delete
4. **Token storage** — confirm tokens are in SecureStore, not AsyncStorage
5. **Rate limit** — 50 login attempts in 10 seconds returns 429
6. **Error check** — a production error shows no stack trace or DB details
