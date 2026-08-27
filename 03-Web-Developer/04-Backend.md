# ⚙️ Backend — API, data and the work that must not be lost

> ⭐⭐ **The frontend can be wrong and you fix it in three minutes. A backend mistake writes bad data
> that outlives the bug.** This file is about the decisions that are expensive to reverse.

---

# 1 · Schema — decisions you cannot cheaply undo

```
□ ⭐ UUIDs FOR PUBLIC IDS. Sequential integers leak volume ("we have
   412 customers") and make enumeration trivial.
□ ⭐⭐ MONEY AS INTEGERS IN THE SMALLEST UNIT. Never a float.
   ⭐ 0.1 + 0.2 !== 0.3. Store 4999, display £49.99.
□ ⭐ TIMESTAMPS AS timestamptz, ALWAYS UTC. Format at render only.
   ⭐⭐ A naive timestamp is a bug waiting for a user in another country.
□ created_at / updated_at on every table
□ ⭐ SOFT DELETE (deleted_at) where the user can delete something —
   "undo" is impossible otherwise, and people delete things by accident
□ ⭐⭐ NOT NULL BY DEFAULT. Every nullable column is a branch you must
   handle forever. Make nullability a decision, not an accident.
□ Foreign keys with explicit ON DELETE behaviour
□ ⭐ CHECK CONSTRAINTS for invariants — balance >= 0, status in (...).
   ⭐⭐ THE DATABASE IS THE LAST LINE THAT CANNOT BE BYPASSED.
□ Enums as a lookup table or a check constraint, not free text
```

```
⭐⭐ EVERY MIGRATION MUST BE REVERSIBLE, AND YOU RUN THEM — NOT THE AGENT.
  · never edit a migration that has run anywhere
  · ⭐ ADDITIVE FIRST: add the column, backfill, switch the code, THEN
    drop the old one. Three deploys, not one.
    ⇒ ⭐⭐ A RENAME IN ONE STEP BREAKS EVERY REQUEST IN FLIGHT.
  · test the migration on a copy of production data, not on 12 rows
  · a destructive migration gets a backup taken first
```

---

# 2 · ⭐⭐ Queries — where it breaks first

```
□ ⭐⭐ EVERY FOREIGN KEY IS INDEXED. Postgres does NOT do this for you.
□ Every column you filter or sort by is indexed
□ ⭐ NO N+1. One query with a join, not one query per row.
   ⭐⭐ 200 queries to render one page is the most common slow page.
□ ⭐ SELECT THE COLUMNS YOU NEED. `select *` ships every column,
   including ones added next year, over the wire and into your DTO.
□ Paginate everything. ⭐ No unbounded list, ever.
□ ⭐⭐ ORDER BY NEEDS A UNIQUE TIEBREAK: `ORDER BY created_at DESC, id DESC`
   ⭐ Without it, equal timestamps can be returned in a different order
     per query ⇒ rows DUPLICATE ACROSS PAGES and others vanish. No
     error. It looks like data corruption and it is a one-line fix.
□ ⭐ EXPLAIN ANALYZE your three slowest queries. "Seq Scan" on a big
   table is your problem.
□ ⭐⭐ TEST WITH 100,000 ROWS. Everything is fast with twelve.
```

---

# 3 · API design

```
□ ⭐ VALIDATE EVERY INPUT AT THE BOUNDARY (Zod). Type, length, range,
   format, allowed values — before anything touches the database.
□ ⭐⭐ NEVER SPREAD req.body INTO A DB CALL.
   ❌ update(req.body)   ⇒ a user posts { is_admin: true }. Mass
                           assignment, and it is written.
   ✅ update({ name: parsed.name })    — whitelist explicitly
□ ⭐⭐ NEVER RETURN THE RAW ROW. Return an explicit shape — it is an
   allow-list, so it also protects the column someone adds later.
□ Consistent error shape: { error: { code, message } }.
   ⭐ Field errors carry the FIELD NAME so the UI can attach them.
□ ⭐ CORRECT STATUS CODES. 400 validation · 401 not logged in ·
   403 logged in but not allowed · 404 · 409 conflict · 422 · 429 · 500
   ⭐⭐ Never 200 with { success: false } — every caller must then
     special-case you.
□ ⭐ EVERY MUTATING ENDPOINT: authenticated, authorised, rate-limited,
   validated. Four things, every time.
□ Version the contract, or at least never break it silently
□ ⭐ GENERATE THE CLIENT TYPES from the API schema if you can. Then a
   renamed field is a build failure instead of "undefined" on screen.
```

---

# 4 · ⭐⭐ Idempotency — the double-charge killer

```
⭐⭐ THE NETWORK LOSES RESPONSES, NOT REQUESTS. A timeout means "I don't
   know", and retrying a create means you may create two.

  ⇒ ⭐ THE CLIENT GENERATES A KEY **ONCE**, at the point of intent —
    when the form opens, not when the button is clicked.
  ⇒ ⭐⭐ THE SERVER STORES IT WITH A UNIQUE CONSTRAINT.
    Same key again ⇒ return the ORIGINAL result. Do not do the work
    twice.

  create unique index on payments (idempotency_key);

⭐ THE BUTTON BEING DISABLED IS UX. THE UNIQUE CONSTRAINT IS THE CONTROL.
```

**Applies to:** payments · signup · sending email/SMS · creating orders · any webhook you receive
(⭐ Stripe **will** deliver the same event twice).

---

# 5 · Background work

```
⭐⭐ IF IT TAKES > ~300ms AND THE USER DOES NOT NEED THE RESULT NOW,
   IT DOES NOT BELONG IN THE REQUEST.
   ⇒ email · PDFs · image processing · AI calls · exports · syncs

□ Return 202 immediately; do the work in a worker
□ ⭐ JOBS ARE IDEMPOTENT — they WILL run twice
□ ⭐⭐ RETRY LIMIT + A DEAD-LETTER QUEUE. A job retrying forever is an
   outage that bills you.
□ ⭐ YOU CAN SEE FAILED JOBS. A queue you cannot inspect is where work
   goes to disappear silently.
□ Scheduled jobs are locked so two instances do not run them at once
```

---

# 6 · Files & storage

```
□ ⭐⭐ VALIDATE BY MAGIC BYTES, NOT BY EXTENSION OR CONTENT-TYPE.
   ⭐ The content-type header is client-supplied. So is the filename.
□ Cap file size at the edge, before it reaches your server
□ ⭐⭐ STRIP EXIF FROM IMAGES — photos carry GPS coordinates. Serving
   one unmodified can publish a user's home address.
□ Resize on upload. Never serve the original 4000px file.
□ ⭐ SERVE USER CONTENT FROM A SEPARATE ORIGIN — an uploaded SVG is a
   document and can contain <script>. On your origin, it runs as you.
□ Signed, expiring URLs for private files. ⭐ Never a public bucket
   with a guessable path.
□ Never trust the filename — generate your own
```

---

# 7 · Third parties

```
□ ⭐ TIMEOUT ON EVERY EXTERNAL CALL. No exceptions.
   ⭐⭐ Without one, a slow dependency holds your connections until you
     have none and your site is down because THEIRS is slow.
□ Retry 5xx/429 only, max 3, exponential backoff + jitter
   ⭐ Jitter matters — without it every client retries in lockstep.
□ ⭐ VERIFY WEBHOOK SIGNATURES BEFORE PROCESSING ANYTHING
□ ⭐⭐ DECIDE THE DEGRADED BEHAVIOUR IN ADVANCE, per dependency
□ Never let a third party's outage block a page load
□ Keys server-side only, rotated, with a spend cap
```

---

# 8 · Observability — you cannot fix what you cannot see

```
□ ⭐⭐ A REQUEST ID ON EVERY REQUEST, returned in the response and
   included in every log line and error report.
   ⭐ THIS IS THE HIGHEST-VALUE 15 LINES IN YOUR WHOLE SETUP: a user's
     screenshot now contains an id you can grep. It is the difference
     between debugging a report and dismissing it.
□ ⭐ STRUCTURED LOGS (JSON), not string concatenation
□ ⭐⭐ NEVER LOG PII — emails, tokens, addresses, card data. Scrub
   before send. Your error tracker is a data store you never designed.
□ Log at the boundaries: request in, external call out, job start/end
□ ⭐ SENTRY — AND TRIGGER A REAL ERROR TO CONFIRM IT ARRIVES.
   Installed is not the same as working.
□ Alert on: error rate, p95 latency, queue depth, failed jobs,
   ⭐⭐ AND SPEND — the alert people forget until the invoice
□ A health check that tests a real dependency, not "the process runs"
```

---

# 9 · ⭐⭐ Sending email — deliverability is not optional

```
⭐⭐ AN EMAIL THAT LANDS IN SPAM IS A FEATURE THAT DOES NOT WORK, AND
   NOTHING IN YOUR LOGS SAYS SO. Your app reports "sent". The user
   never got the password reset. They leave.

⭐ THE THREE DNS RECORDS. ALL THREE, OR YOU ARE IN SPAM:
  ① ⭐ SPF   — which servers may send as your domain
  ② ⭐ DKIM  — a signature proving the mail was not altered
  ③ ⭐⭐ DMARC — what to do when SPF/DKIM fail, and WHERE TO SEND
         REPORTS. ⭐ Start at p=none and READ the reports before
         moving to quarantine or reject.
  ⇒ ⭐ YOUR EMAIL PROVIDER GIVES YOU THESE RECORDS. ADDING THEM IS
    TEN MINUTES AND IT IS THE DIFFERENCE BETWEEN ARRIVING AND NOT.
```

```
□ ⭐⭐ SEND FROM A SUBDOMAIN (mail.yoursite.com) — so a
   deliverability problem does not damage your main domain's reputation
□ ⭐ SEPARATE TRANSACTIONAL FROM MARKETING — different subdomains,
   different reputations. ⭐⭐ ONE BAD CAMPAIGN MUST NOT STOP PASSWORD
   RESETS FROM ARRIVING.
□ ⭐ A REAL FROM ADDRESS THAT ACCEPTS REPLIES. "noreply@" is a bad
   default and some filters treat it as a signal.
□ ⭐⭐ HANDLE BOUNCES AND COMPLAINTS — keep sending to a dead
   address and your reputation falls for everyone.
□ ⭐ WARM UP A NEW DOMAIN. Do not send 10,000 emails on day one.
□ ⭐ TEST WITH mail-tester.com OR SIMILAR BEFORE LAUNCH — it scores
   you and names what is missing.
□ ⭐⭐ SEND YOURSELF A PASSWORD RESET AT GMAIL, OUTLOOK **AND** A
   CORPORATE DOMAIN. ⭐ They filter very differently.
□ Plain-text alternative alongside the HTML
□ ⭐ UNSUBSCRIBE + POSTAL ADDRESS ON MARKETING MAIL — legal, not
   optional (→ [12-Legal-and-Compliance.md §6](12-Legal-and-Compliance.md))
```

---

# 10 · The engineering basics an agent will skip

```
⭐⭐ IT WILL BUILD THE FEATURE. IT WILL NOT ADD THESE UNLESS YOU ASK.

  □ pagination on a list endpoint
  □ ⭐ AN INDEX for the column it just started filtering on
  □ ⭐⭐ A TRANSACTION around two writes that must both happen
  □ a timeout on the HTTP call it just added
  □ rate limiting on the endpoint it just created
  □ ⭐ THE UNIQUE CONSTRAINT behind the idempotency key it just used
  □ ON DELETE behaviour for the foreign key it just added
  □ ⭐⭐ WHAT HAPPENS ON THE SECOND CLICK
  □ the empty and error states for the list it just returned

⭐ ADD THESE TO CLAUDE.md AS A "BEFORE YOU FINISH" LIST, or you will
  ask for them one at a time forever.
```

---

**Back:** [folder index](README.md) · **Security:** [05-Security.md](05-Security.md) ·
**Scale:** [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md) ·
**AI features:** [13-AI-Features.md](13-AI-Features.md)
