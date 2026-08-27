# 🔒 Security — is customer data actually safe?

> ⭐⭐ **The question to answer is not "did I add security". It is "if someone hostile spends ten
> minutes on my site, what do they get?"** This file is the audit that answers it. Run it before
> launch and after any change to auth, data or payments.

**The one sentence:** the browser is not a security boundary. **Everything that matters is enforced
on the server, or it is not enforced.**

---

# 0 · ⭐⭐ The 60-second exposure check — run on every deploy

```bash
# ⭐⭐ Both must return 404. If either returns 200, everything is compromised.
curl -s -o /dev/null -w "%{http_code}\n" https://yoursite.com/.env
curl -s -o /dev/null -w "%{http_code}\n" https://yoursite.com/.git/config

# ⭐ No secret in the built bundle — anything here is public forever
grep -rEn "sk_live|sk_test|service_role|secret|api[_-]?key|BEGIN PRIVATE" \
     .next/ dist/ build/ 2>/dev/null

# ⭐ Headers present
curl -sI https://yoursite.com | grep -iE "strict-transport|content-security|x-content-type"
```

> ⭐⭐ **A readable `/.env` is total compromise** — database, payments, email, everything, in one
> request. It happens when a build copies the file into a public directory. **Check it every deploy,
> not once.**

---

# 1 · ⭐⭐ Authorization — the leak that actually happens

```
⭐⭐ THIS IS #1. NOT XSS, NOT SQL INJECTION. **IDOR** — a logged-in user
   changing an ID and getting someone else's data.

   GET /api/invoices/1042   ⇒ yours ✅
   GET /api/invoices/1043   ⇒ ⭐⭐ SOMEONE ELSE'S. NO ERROR ANYWHERE.

⭐ WHY IT SURVIVES REVIEW: the UI only ever links to your own rows, so
  it looks completely correct in the browser. THE BUG IS ONLY VISIBLE
  IF YOU GO LOOKING.
```

```
□ ⭐⭐ THE ID-SWAP TEST — 60 seconds, do it before every launch:
   ① log in as user A, note an id from a URL or the network tab
   ② log in as user B (a second seeded account)
   ③ request A's id as B — page, API call, and any export/download
   ④ ⭐ MUST BE 403 OR 404. Anything else is a live data leak.

□ ⭐ EVERY QUERY FILTERS BY THE AUTHENTICATED USER, SERVER-SIDE.
   Not the UI. Not a query parameter the client sent. The session.
   ❌ where('user_id', req.body.userId)     ⇒ ⭐⭐ CLIENT-SUPPLIED. NO.
   ✅ where('user_id', session.userId)

□ ⭐⭐ RLS ON **AND FORCED** IN SUPABASE.
   ⭐ The app filter and RLS are TWO LAYERS. RLS catches the query
     someone writes next year and forgets to filter.
   alter table x enable row level security;
   alter table x force  row level security;

□ Roles checked server-side. ⭐ A hidden button is UX, never permission.
□ ⭐ LIST ENDPOINTS TOO — /api/orders returning everyone's orders is the
   same bug at scale, and it is easy to miss because the UI filters.
□ Search and autocomplete endpoints — ⭐⭐ frequently the one nobody
   scoped to the user.
□ ⭐ FOR SENSITIVE IDS, RETURN 404 NOT 403. A 403 confirms the record
   exists.
```

---

# 2 · Never return a whole row

```
⭐⭐ HIDING A FIELD IN THE UI IS NOT REDACTING IT.

   The page shows a name. The response contains:
     { id, name, email, phone, address, stripe_customer_id,
       internal_notes, password_hash, is_admin }
   ⇒ ⭐ IT IS IN THE NETWORK TAB, THE BROWSER CACHE, AND ANY PROXY.

⭐⭐ RETURN AN EXPLICIT SHAPE. It is an ALLOW-LIST, which means it also
  protects the field somebody adds next year.
```

```
□ Every endpoint returns a named shape, not `select *`
□ ⭐ Open DevTools and READ YOUR OWN RESPONSES. You will find something.
□ Error responses do not leak internals — no stack traces, no SQL, no
   file paths in production
□ ⭐⭐ A 403 body must not contain the resource you were denied
```

---

# 3 · Secrets

```
□ ⭐⭐ NEVER `NEXT_PUBLIC_` ANYTHING SECRET. The prefix EXPOSES — it is
   a warning label, not a guard. Anything in the bundle is public
   forever: git history, CDN caches, archives.
   ⇒ ⭐ IF ONE LEAKS, ROTATE IT. Deleting the code does nothing.
□ ⭐ THE SUPABASE service_role KEY NEVER TOUCHES CLIENT CODE.
□ .env in .gitignore before the first commit
□ Different keys per environment; production keys nowhere near local
□ GitHub secret scanning + push protection ON
□ ⭐⭐ IF A CALL NEEDS A SECRET, THE BROWSER CALLS **YOUR** API AND YOUR
   API HOLDS THE KEY. That is also where you put the rate limit and
   the spend cap.
   ⭐ A third-party API key in a frontend is scraped by bots in hours.
```

---

# 4 · Input & endpoints

```
□ ⭐ EVERY INPUT VALIDATED AT THE BOUNDARY (Zod). Type, length, range,
   format, allowed values.
□ ⭐⭐ NEVER SPREAD req.body INTO A DB CALL.
   ❌ update(req.body)  ⇒ ⭐ a user posts { is_admin: true } and it is
                          written. This is mass assignment.
   ✅ update({ name: parsed.name, bio: parsed.bio })
□ Parameterised queries only — never string-concatenate SQL
□ ⭐ FILE UPLOADS: check MAGIC BYTES not the extension · cap the size ·
   ⭐⭐ STRIP EXIF (photos carry GPS — serving one unmodified can publish
   someone's home address) · serve from a SEPARATE ORIGIN so an
   uploaded SVG containing script cannot run as your origin
□ Server-side redirects validated against an allow-list (open redirect)
□ ⭐ NO USER INPUT IN A SERVER-SIDE fetch() URL without an allow-list —
   SSRF. ⭐⭐ Especially 169.254.169.254, the cloud metadata endpoint.
□ Webhooks verify signatures before doing anything
```

---

# 5 · Auth & session

```
□ ⭐⭐ TOKEN IN AN HttpOnly COOKIE, NOT localStorage.
   ⭐ localStorage is readable by any script on the page — including one
     from a compromised dependency. With XSS + localStorage, the token
     is stolen and used from the attacker's machine until it expires.
     With an HttpOnly cookie the attacker can act only while the page
     is open, and revoking the session ends it.
   ⇒ ⭐⭐ NEITHER IS XSS-PROOF. THE COOKIE BOUNDS THE BLAST RADIUS.
□ Cookies: Secure, HttpOnly, SameSite=Lax
   ⭐ Check in the Application tab on the LIVE site — Secure cookies
     silently do not set over plain HTTP.
□ ⭐ ONCE YOU USE COOKIES, CSRF PROTECTION IS BACK ON THE TABLE.
   SameSite=Lax kills most of it, but never mutate state on a GET.
□ Rate-limit login, password reset and email change HARD
□ Re-authenticate before changing email, password, or payment details
□ Sessions expire; logout revokes server-side
□ ⭐⭐ LOGOUT CLEARS THE CLIENT CACHE — otherwise the next user on a
   shared machine sees the previous user's data render from cache
□ MFA available on anything that handles money or personal data
```

---

# 6 · Payments

```
□ ⭐⭐ VERIFY THE STRIPE WEBHOOK SIGNATURE BEFORE PROCESSING ANYTHING.
   ⭐ Without it, anyone who finds the URL can POST a fake
     "payment succeeded".
□ ⭐ AMOUNTS COME FROM THE SERVER, NEVER THE CLIENT. A client-sent
   price is a price the client chooses.
□ ⭐⭐ IDEMPOTENCY KEY ON EVERY CHARGE + a unique constraint behind it.
   ⭐ A double-click, a retry, or a duplicated webhook must not charge
     twice. The button being disabled is UX, not the control.
□ Webhooks are idempotent — Stripe WILL deliver the same event twice
□ Money as integers in the smallest unit. Never floats.
□ Never store card data. Never log it.
□ Test the failure paths: declined, 3DS, timeout, duplicate webhook
```

---

# 7 · Headers & transport

```
□ HTTPS everywhere, HSTS on, no mixed content
□ ⭐⭐ CSP — the highest-value header, because it ASSUMES YOU WILL HAVE
   AN XSS BUG (with hundreds of dependencies, that is realistic).
   · script-src 'self'  ⇒ injected inline script does not run at all
   · ⭐ connect-src ...  ⇒ even if script runs, IT CANNOT SEND THE DATA
     ANYWHERE. That is egress filtering for the browser.
   ⇒ ⭐⭐ 'unsafe-inline' DISABLES ESSENTIALLY ALL OF IT — and it is
     also the easiest way to make a CSP "work". Deploy Report-Only
     first, collect violations for a week, then enforce.
□ X-Content-Type-Options: nosniff
□ Referrer-Policy: strict-origin-when-cross-origin
   ⭐ Otherwise full URLs — with ids in them — leak to third parties
□ frame-ancestors 'none' (clickjacking)
□ Cloudflare WAF + bot protection on before launch
```

---

# 7b · ⭐⭐ Verify the headers from outside — do not assume

```
⭐⭐ YOU SET THE HEADERS. NOW PROVE THEY ARRIVED. Two free scans,
   two minutes, and they grade you A–F.

  ① ⭐ https://www.ssllabs.com/ssltest/   ⇒ TARGET **A or A+**
     TLS version, cipher suites, certificate chain, known weaknesses.
     ⭐ A misconfigured chain works in Chrome and FAILS on older
       Android — which you will never see on your own machine.

  ② ⭐ https://securityheaders.com/       ⇒ TARGET **A**
     CSP, HSTS, X-Content-Type-Options, Referrer-Policy,
     Permissions-Policy. ⭐ It names exactly what is missing.

⭐⭐ READ THE GRADE HONESTLY — BOTH ARE CONFIGURATION CHECKS:
  · ⭐ A+ ON TLS SAYS NOTHING ABOUT WHETHER STRANGERS CAN READ YOUR
    CUSTOMERS' DATA. That is §1, and no scanner tests it.
  · ⭐⭐ A CSP CONTAINING 'unsafe-inline' CAN STILL SCORE WELL WHILE
    HAVING DISABLED THE PROTECTION YOU WANTED. The grade does not
    know what you meant.
⇒ ⭐ A GRADE IS A FLOOR, NOT A CERTIFICATE.
```

⭐ **Full tool table:** [Reference/Verification-Tools.md](Reference/Verification-Tools.md)

---

# 8 · Supply chain — the XSS you did not write

```
□ ⭐⭐ VERIFY EVERY PACKAGE THE AGENT ADDS EXISTS AND IS THE RIGHT ONE.
   ⭐ Models invent plausible package names; attackers register them.
     This is not theoretical.
   ⇒ check: weekly downloads · last publish · does the repo link
     resolve · its own dependency count
□ Commit the lockfile; build with a frozen install in CI
□ `npm audit --omit=dev` in CI, fail on high
□ Dependabot on, updates as reviewable PRs
□ ⭐ DO NOT AUTO-ADOPT BRAND-NEW VERSIONS. A compromised release is
   usually pulled within hours; waiting is free protection.
□ ⭐⭐ devDependencies MATTER — they never ship, but they RUN IN CI
   where your tokens and source are.
□ ⭐ ADD FEWER PACKAGES. The only control with no ongoing cost.
```

---

# 9 · XSS & user content

```
□ ⭐ React escapes {values} — that covers most of it. The holes:
   ① ⭐⭐ dangerouslySetInnerHTML  ⇒ sanitise (DOMPurify) or don't
   ② ⭐ <a href={userUrl}>        ⇒ React does NOT validate this.
      "javascript:..." executes. Allow-list http/https.
   ③ spreading unknown props onto an element
   ④ ⭐⭐ user-uploaded SVG served from your origin — it is a document
      and can contain <script>
□ Markdown renderers: check whether raw HTML is enabled by default
□ ⭐ NEVER put user input into eval, new Function, or a template that
   becomes code
```

---

# 10 · ⭐⭐ Privacy & compliance

```
□ ⭐ COLLECT LESS. The data you do not store cannot leak.
□ ⭐⭐ NEVER LOG PII — emails, names, addresses, tokens, card data —
   to logs, to Sentry, or to analytics. Scrub before send.
   ⭐ Your error tracker becomes a data store you never designed, and
     it is usually the least-protected one you own.
□ Privacy policy, terms, cookie notice — published before launch if you
   collect anything at all
□ A real way to delete an account and its data
□ Analytics: respect Do Not Track; no PII in event properties
□ ⭐ IF YOU TAKE HEALTH, FINANCIAL OR CHILDREN'S DATA, the rules are
   stricter than this file. Get advice.
```

---

# ⭐⭐ The verdict — can I say "customer data is secure"?

**Only if all six are true and you have tested them, not assumed them:**

```
① ⭐⭐ THE ID-SWAP TEST PASSES — you ran it, as two real users
② RLS IS ON AND FORCED
③ NO SECRET IS IN THE CLIENT BUNDLE — you grepped the build
④ /.env AND /.git/config RETURN 404 — you curled them
⑤ EVERY INPUT IS VALIDATED SERVER-SIDE, AND NOTHING SPREADS req.body
⑥ UPLOADS ARE VALIDATED BY CONTENT AND STRIPPED OF EXIF

⭐ THE HONEST SENTENCE: "Authorization is enforced server-side and I
  have tested it with two accounts. RLS is on. There are no secrets in
  the bundle. I have not had it penetration-tested."
  ⇒ ⭐⭐ THAT IS CREDIBLE. "It's secure" is not.
```

---

**Back:** [folder index](README.md) · **Cost & abuse:** [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md) ·
**The audit:** [10-Ship-Checklist.md §8](10-Ship-Checklist.md)
