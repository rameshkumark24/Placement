# 🔒 Security Checklist — Vibe-Coded Apps

> **OWASP Top 10:2025** — #1 Broken Access Control (SSRF merged into it), #2 Security
> Misconfiguration, #3 Software Supply Chain Failures. All three are precisely where AI-generated
> code is weakest, because an agent optimises for "it works", not "it can't be abused".

Run this before every launch, and again after any change to auth, roles, or data access.

---

## The 60-second pre-launch test

If you only do five things, do these:

1. **IDOR test** — log in as user A, change an ID in the URL/request to user B's resource. Expect 403.
2. **Secret scan of git history** — `git log -p | grep -iE 'sk-|api[_-]?key|password|secret'`
3. **Client bundle check** — search the built JS for your service-role key. It must not be there.
4. **Rate limit check** — hit your login endpoint 50 times in 10 seconds. Expect 429.
5. **Error check** — trigger a 500 in production. Expect no stack trace, no SQL, no table names.

---

## Authentication

- [ ] Passwords hashed with **bcrypt or argon2** — never plain, never MD5/SHA1
- [ ] Use a proven auth provider rather than hand-rolling (Supabase Auth, Clerk, Auth.js)
- [ ] Session / JWT expiry set short; refresh token rotation enabled
- [ ] Session revocation actually works — logout invalidates server-side, not just clears localStorage
- [ ] Login endpoint rate limited; account lockout or exponential backoff after failures
- [ ] Password reset tokens: single-use, short expiry, invalidated on use
- [ ] **No account enumeration** — signup, login and reset return identical messages whether or not
      the email exists
- [ ] Email verification on signup
- [ ] 2FA available for accounts holding anything valuable
- [ ] JWTs stored in httpOnly cookies where possible, not localStorage (XSS steals localStorage)

---

## Authorization — the IDOR layer

**This is the section that matters most.** Broken access control is how vibe-coded apps leak data.

- [ ] **Every** query filters by the authenticated user's ID — server-side, in the query itself
- [ ] Row Level Security enabled on every table (Supabase/Postgres RLS), policies **tested with a
      second real account**
- [ ] IDOR tested manually: user A swaps an ID to user B's resource → 403, not 200
- [ ] Admin routes behind a separate server-side guard, not just a hidden UI link
- [ ] **Mass assignment blocked** — whitelist updatable fields; never spread the request body into
      the ORM (`{...req.body}` lets a user set `role: "admin"`)
- [ ] No authorization decision made in the browser
- [ ] Role checks happen on the server on every request, not once at login
- [ ] Object ownership verified on update and delete, not just read

```ts
// 💀 The classic AI-generated IDOR
const note = await db.note.findUnique({ where: { id: params.id } });
return note;                              // any user can read any note

// ✅ Ownership enforced in the query
const note = await db.note.findFirst({
  where: { id: params.id, userId: session.user.id },
});
if (!note) return new Response('Not found', { status: 404 });
```

---

## Data & secrets

- [ ] All keys in env vars; nothing hardcoded
- [ ] `.env` in `.gitignore` **before** the first commit
- [ ] Secrets scanned across **git history**, not just current files — a leaked key stays in history
      forever. **Rotate it, don't just delete it.**
- [ ] Service-role / admin keys never shipped in the client bundle
      (in Next.js: no secret in a `NEXT_PUBLIC_*` var)
- [ ] HTTPS enforced; HSTS header set
- [ ] Security headers: CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy
- [ ] CORS restricted to known origins — **not `*`**
- [ ] Sensitive fields encrypted at rest where warranted
- [ ] PII never written to logs
- [ ] Database backups encrypted and stored separately

---

## Input & endpoints

- [ ] Parameterised queries / ORM only — no string-concatenated SQL
- [ ] Input validated at the API boundary (Zod / Pydantic), server-side, on every endpoint
- [ ] Output escaped; avoid `dangerouslySetInnerHTML` and equivalents
- [ ] CSRF protection on cookie-based sessions
- [ ] File uploads: type allowlist, size cap, filename sanitised, served from a separate origin
- [ ] Rate limiting per IP **and** per user on all write and auth endpoints
- [ ] Webhook signatures verified before processing
- [ ] Production error responses carry no stack traces, SQL, or table names
- [ ] Debug endpoints, seed routes and default credentials removed before launch
- [ ] SSRF: any URL supplied by a user is validated against an allowlist before you fetch it
      (blocks `http://169.254.169.254` metadata attacks)

---

## Supply chain

*AI hallucinates package names; attackers pre-register them. This is called slopsquatting.*

- [ ] **Every** package the agent adds is verified: does it exist, who maintains it, real download
      numbers, recent commits
- [ ] Package name read character by character — `reqeusts` vs `requests`, `crossenv` vs `cross-env`
- [ ] Lock file committed; no blind auto-upgrades
- [ ] `npm audit` / `pip-audit` / Dependabot / Snyk running on every build
- [ ] Licence check on every dependency
- [ ] Minimal dependency count — every package is attack surface you didn't write

---

## If the app calls an LLM

- [ ] Per-user quotas and a hard spend cap
      (see [API-Safety-and-Cost-Control.md](API-Safety-and-Cost-Control.md#8-spend-caps--billing-alerts))
- [ ] Model output **never** trusted as code — no `eval`, no shell, no SQL interpolation
- [ ] Prompt injection handled: user content delimited, system prompt states it is data not instructions
- [ ] Output filtered before display (it can emit XSS payloads)
- [ ] PII not sent to third-party models without consent and a DPA
- [ ] The model never receives secrets or other users' data in its context

---

## AI-generated code review gate

Before merging anything an agent wrote, check each endpoint for:

| Check | Why |
|---|---|
| Auth check present | Agents forget it on "internal" routes |
| Ownership check present | The IDOR default |
| Input validated server-side | Agents validate on the client and call it done |
| No secrets inline | Agents paste example keys that become real ones |
| No `SELECT *` unbounded | Becomes a performance and data-exposure issue |
| Error messages generic | Agents echo DB errors straight to the user |
| Rate limit applied | Agents never add one unless told |

---

## Tools

| Tool | Purpose |
|---|---|
| `gitleaks` / `trufflehog` | Secret scanning across git history |
| `npm audit`, `pip-audit` | Dependency vulnerabilities |
| Dependabot / Snyk | Continuous dependency monitoring |
| OWASP ZAP | Automated web vulnerability scan |
| Burp Suite (Community) | Manual IDOR / auth testing |
| `securityheaders.com` | Header configuration grade |
| Supabase RLS policy tests | Row-level security verification |
