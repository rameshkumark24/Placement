# 🔒 Security — is customer data safe on a device you don't control?

> ⭐⭐ **Mobile adds a threat the web does not have: the attacker has the app.** They can unzip it,
> read every string, proxy the traffic, and on a rooted device read your local storage. **Everything
> that matters is enforced on the server, and everything stored locally must survive being read.**

---

# 0 · ⭐⭐ The exposure check — unzip your own build

```bash
# ⭐⭐ ANYTHING THIS FINDS IS PUBLIC. FOREVER.
unzip -p app.apk | strings | grep -iE "sk_live|sk_test|secret|api[_-]?key|BEGIN PRIVATE"

# iOS: extract the .ipa and grep the binary and any bundled JS
unzip app.ipa && strings Payload/*.app/* | grep -iE "sk_live|secret"

# ⭐ ALSO CHECK THE JS BUNDLE — Expo apps ship it inside
```

```
⭐⭐ THERE ARE NO SECRETS IN A MOBILE APP.
   ⭐ Obfuscation is not encryption. Minification is not protection.
     Someone with an APK and ten minutes has every string you shipped.

  ✅ FINE IN THE APP: your API base URL · a publishable/anon key that
     is designed to be public (Stripe pk_, Supabase anon with RLS on) ·
     the Sentry DSN
  ❌ NEVER: a service_role key · sk_live/sk_test · any API key for a
     paid third party · a signing key · a database URL

  ⇒ ⭐⭐ IF A CALL NEEDS A SECRET, THE APP CALLS **YOUR** API AND
    **YOUR** API HOLDS IT — which is also where the rate limit and
    the spend cap live.
```

---

# 1 · ⭐⭐ Local storage — the mobile-only decision

```
⭐⭐ AsyncStorage IS **PLAIN TEXT ON DISK**.
   ⭐ Readable on a rooted or jailbroken device, and sometimes in
     device backups. It is not a vault. It is a file.

  ✅ expo-secure-store  ⇒ ⭐ iOS Keychain / Android Keystore.
     ⭐⭐ TOKENS, REFRESH TOKENS, ANYTHING PERSONAL, ANYTHING THAT
       WOULD MATTER IF READ.
     ⭐ Note: small values only. It is not for bulk data.
  ✅ MMKV / AsyncStorage ⇒ ⭐ preferences, cached non-sensitive
     content, UI state. ⭐⭐ NOTHING PERSONAL.
  ✅ ⭐ AN ENCRYPTED DB (SQLCipher / MMKV with encryption) if you cache
     real user data offline.

□ ⭐⭐ THE QUERY CACHE COUNTS. If you persist TanStack Query to disk,
   you have just written user data to storage — decide where.
□ ⭐ LOGOUT CLEARS ALL OF IT: secure store, cache, query cache, files,
   downloaded images. ⭐⭐ Otherwise the next person to use the phone
   sees the previous user's data.
□ Anything cached offline should expire
```

---

# 2 · ⭐⭐ Authorization — still the #1 real leak

```
⭐⭐ IDOR: a logged-in user changes an ID and gets someone else's data.
   ⭐ The app only ever requests your own ids, so it looks perfectly
     correct — until someone proxies the traffic and edits one.

□ ⭐⭐ THE ID-SWAP TEST — do it through a PROXY, not the app:
   ① run the app through Charles/Proxyman/mitmproxy
   ② capture a request, note the id
   ③ log in as a second user, replay the first user's id
   ④ ⭐ MUST BE 403 OR 404
   ⇒ ⭐⭐ IF YOU HAVE NEVER PROXIED YOUR OWN APP, DO IT ONCE. It is
     the fastest way to understand what an attacker sees.

□ Every query filters by the SESSION user, server-side. Never by an
   id the client sent.
□ RLS on and forced
□ ⭐ A HIDDEN BUTTON IS NOT A PERMISSION. Roles checked server-side.
□ ⭐⭐ NEVER RETURN A WHOLE ROW. An explicit shape is an allow-list.
□ List and search endpoints scoped too
```

---

# 3 · Transport

```
□ HTTPS only. ⭐ No cleartext traffic exception in the Android config.
□ ⭐⭐ CERTIFICATE PINNING IF THE DATA IS SENSITIVE.
   ⭐ Without it, anyone can proxy your traffic with a self-signed
     cert in five minutes and read every request and response.
   ⭐ The cost: a certificate rotation can brick your app if you do
     not ship a backup pin. Pin the CA or ship two pins.
□ ⭐ TEST WHAT AN ATTACKER SEES — run the app through a proxy once and
   read your own traffic. It is a genuinely useful hour.
□ No sensitive data in URLs — ⭐ they land in server logs and analytics
□ Timeouts on everything
```

---

# 3b · ⭐ Scan the API domain — people skip this

```
⭐⭐ "IT'S A MOBILE APP, THERE'S NO WEBSITE" IS WRONG. YOUR API IS A
   WEBSITE, AND IT IS WHERE THE DATA IS.

  ① ⭐ https://www.ssllabs.com/ssltest/  ⇒ point it at your API
     domain. TARGET **A or A+**.
     ⭐⭐ AND IF YOU PIN CERTIFICATES, CHECK THE EXPIRY HERE — a
       rotation you did not plan for BRICKS EVERY INSTALLED APP,
       and no OTA update can fix it.
  ② ⭐ https://securityheaders.com/  ⇒ your API, and your
     privacy-policy page (which must be live for the stores anyway).

⭐ A GRADE IS A FLOOR. Neither scan tests whether user A can read
  user B's data — that is §2, and it needs you and a proxy.
```

⭐ **Full tool table:** [Reference/Verification-Tools.md](Reference/Verification-Tools.md)

---

# 4 · ⭐⭐ Permissions

```
⭐⭐ PERMISSIONS ARE BOTH A PRIVACY AND A REJECTION ISSUE.

□ ⭐ ASK AT THE MOMENT OF USE, NEVER ON LAUNCH.
   ⭐⭐ A PERMISSION PROMPT ON FIRST OPEN IS THE #1 REASON PEOPLE DENY
     — they have no context yet, so they say no, and now your feature
     is dead for that user.
□ ⭐ THE USAGE STRING EXPLAINS **WHY**, in plain language.
   ❌ "This app needs camera access."      ⇒ ⭐ REJECTED
   ✅ "To scan the barcode on your receipt."
□ ⭐⭐ THE SCREEN MUST STILL WORK IF DENIED. Degrade, do not dead-end.
□ ⭐ ASK FOR THE LEAST: the photo PICKER instead of full library
   access · coarse location instead of precise · no background
   location unless it is genuinely the product
□ Handle "denied forever" — ⭐ offer a link to Settings
□ ⭐⭐ EVERY PERMISSION YOU REQUEST MUST APPEAR IN THE STORE PRIVACY
   FORM. A mismatch is a rejection or a later removal.
```

---

# 5 · Payments — the rejection rule

```
⭐⭐ THIS IS THE #1 STORE REJECTION AND IT IS BINARY:

   DIGITAL goods, subscriptions, in-app content
     ⇒ ⭐ MUST USE APPLE / GOOGLE IAP. (~15–30% commission.)
   PHYSICAL goods, real-world services
     ⇒ ⭐ MUST **NOT** USE IAP. Stripe or similar.

  ⭐⭐ GETTING IT BACKWARDS IS AN AUTOMATIC REJECTION AND COSTS YOU A
    REVIEW CYCLE EACH TIME. Decide before you build.

□ ⭐ VALIDATE RECEIPTS SERVER-SIDE. A client-side "purchase succeeded"
   is trivially faked.
□ Restore purchases works — ⭐ required by Apple
□ Idempotency on every payment and every webhook
□ ⭐ AMOUNTS COME FROM THE SERVER
□ Never store card data. Never log it.
□ Test declined, 3DS, timeout, and duplicate delivery
```

---

# 6 · Input & the app's own surfaces

```
□ ⭐ VALIDATE EVERY INPUT SERVER-SIDE (Zod at the boundary)
□ ⭐⭐ NEVER SPREAD req.body INTO A DB CALL — whitelist fields
□ ⭐ DEEP LINKS ARE UNTRUSTED INPUT FROM ANYONE.
   ⭐⭐ ANY APP OR WEBSITE CAN OPEN YOUR APP WITH ANY URL. Validate
     every parameter, and never let a deep link perform an action
     without confirmation.
□ ⭐ WEBVIEWS: disable JS if you can, allow-list the origins, never
   inject a token into one. ⭐⭐ A WebView is a browser you shipped.
□ ⭐ CLIPBOARD: do not copy secrets to it — other apps can read it
□ Uploads: validate content, cap size, ⭐⭐ STRIP EXIF (GPS)
```

---

# 7 · Privacy & what leaves the device

```
□ ⭐⭐ CRASH REPORTS AND ANALYTICS LEAVE THE DEVICE. Scrub PII in
   beforeSend. ⭐ Your error tracker is a data store you never
   designed, and usually the least-protected one you own.
□ ⭐ THE DATA-SAFETY / PRIVACY-NUTRITION FORM MUST MATCH REALITY —
   ⭐⭐ INCLUDING WHAT YOUR SDKs COLLECT WITHOUT YOU ASKING. Analytics
   and ad SDKs collect more than you think.
□ Privacy policy live and reachable — ⭐ a 404 is a rejection
□ ⭐ A REAL WAY TO DELETE AN ACCOUNT AND ITS DATA — Apple requires it
   if you allow account creation
□ ⭐ COLLECT LESS. What you do not store cannot leak.
□ Screenshots in the app switcher — ⭐ blur sensitive screens when
   backgrounded if the data warrants it
```

---

# 8 · Supply chain

```
□ ⭐⭐ VERIFY EVERY PACKAGE THE AGENT ADDS EXISTS AND IS THE RIGHT ONE.
   ⭐ Models invent plausible names; attackers register them.
□ ⭐⭐ AND ASK: DOES IT ADD NATIVE CODE? That is a second question with
   its own cost — it ends OTA for that change.
□ Commit the lockfile; frozen install in CI
□ ⭐ A NATIVE DEPENDENCY IS A BIGGER COMMITMENT than a JS one — it is
   harder to remove and it can block an SDK upgrade
□ Audit in CI, Dependabot on
□ ⭐ Prefer Expo SDK modules — already vetted and already in the build
```

---

# ⭐⭐ The verdict — can I say customer data is safe?

```
ONLY IF ALL SEVEN ARE TRUE AND TESTED, NOT ASSUMED:

 ① ⭐⭐ THE ID-SWAP TEST PASSES — run through a proxy, as two users
 ② ⭐⭐ TOKENS ARE IN SECURE STORE, not AsyncStorage
 ③ ⭐ YOU UNZIPPED YOUR OWN BUILD AND FOUND NO SECRETS
 ④ RLS IS ON AND FORCED
 ⑤ PERMISSIONS ARE ASKED AT POINT OF USE AND DENIAL IS HANDLED
 ⑥ UPLOADS ARE VALIDATED AND EXIF-STRIPPED
 ⑦ LOGOUT CLEARS EVERYTHING LOCAL

⭐ THE HONEST SENTENCE: "Authorization is server-side and I tested it
  through a proxy with two accounts. Tokens are in the Keychain.
  I unzipped my own build and there are no secrets in it. I have not
  had it penetration-tested."
  ⇒ ⭐⭐ THAT IS CREDIBLE. "It's secure" is not.
```

---

**Back:** [folder index](README.md) · **Audit:** [10-Ship-Checklist.md §6](10-Ship-Checklist.md) ·
**Web equivalent:** [`03-Web-Developer/05-Security.md`](../03-Web-Developer/05-Security.md)
