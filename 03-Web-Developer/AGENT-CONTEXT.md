# 🌐 Web — Agent Context

> **This file is the memory.** Paste it into a new chat (or keep it in Notion and link it) before
> asking for any web work. It is written **to the agent**, not to me. It is self-sufficient: it works
> alone, and every other file in this folder is depth behind a line in here.

---

# 0 · How I work

```
I VIBE CODE. You write most of the code. That changes what I need from you:

  ⭐ I will not catch a subtle bug by reading 400 lines of your output.
     ⇒ so KEEP DIFFS SMALL and TELL ME WHAT TO LOOK AT.
  ⭐ I cannot review what I did not ask for.
     ⇒ so NEVER touch files outside the task.
  ⭐ The failures that hurt me are SILENT: a bill, a data leak, a broken
     page that still returns 200.
     ⇒ so PRICE AND SECURITY GET CHECKED WITHOUT ME ASKING.
```

**Non-negotiable working rules:**

1. **Plan before code on anything non-trivial.** More than one file, or any auth/payment/data
   change → plan first, wait for approval. See §1.
2. **Never run `git push`, `git reset --hard`, `git rebase`, or any migration.** Write them; I run
   them.
3. **Never add a dependency without naming it and saying why.** You invent package names;
   attackers register them.
4. **Never refactor code I did not ask you to change.** Say "this file also has X" and stop.
5. **Never delete or weaken a test to make something pass.**
6. **If you are unsure, say so.** A wrong confident answer costs me hours. "I don't know, here's how
   to check" is a good answer.

---

# 1 · The loop (follow this every time)

```
 ① PLAN     ⭐ complex task ⇒ PLAN MODE. No code. I read and approve it first.
 ② COMMIT   I checkpoint. (If I forgot, remind me — one line, then continue.)
 ③ BUILD    One feature. Small diff. Stop at the edge of the task.
 ④ EXPLAIN  Tell me what changed and what to look at. Flag anything risky.
 ⑤ REVIEW   /code-review  → correctness, simplification
 ⑥ SECURE   /security-review → if it touched auth, data, payments, uploads or env
 ⑦ CROSS    ⭐⭐ CODEX SECOND OPINION on anything that touches money, auth or
            customer data. Different model, different blind spots. See §2.
 ⑧ TEST     Prove it works AND prove the failure path works.
 ⑨ SHIP     Only after §9's checklist for anything user-facing.
```

**When to use plan mode — do not skip this judgement:**

| Task | Mode |
|---|---|
| Rename a variable, fix a typo, adjust padding | ⭐ Just do it |
| One component, one endpoint, clear spec | ⭐ Do it, then explain |
| **Touches auth, payments, or user data** | ⭐⭐ **PLAN FIRST** |
| **More than ~3 files, or a schema change** | ⭐⭐ **PLAN FIRST** |
| **"Make it faster" / "make it scale" / "fix the design"** | ⭐⭐ **PLAN FIRST** — these are ambiguous and you will guess wrong |

**A good plan says:** what changes, which files, what could break, what I have to do myself
(migrations, env vars, dashboard settings), and what you are *not* doing.

---

# 2 · ⭐⭐ The Codex cross-check

```
⭐⭐ ONE MODEL REVIEWING ITS OWN CODE IS A WEAK CHECK. It shares the blind
   spot that produced the bug.

RUN A SECOND MODEL (Codex / GPT) AS A FINAL CHECK WHEN THE DIFF TOUCHES:
   · money        · authentication / authorization    · customer data
   · file upload  · anything that sends email or SMS  · a public endpoint

THE PROMPT TO GIVE THE SECOND MODEL — do not ask "is this good?":

   "Here is a diff. Find bugs. Specifically check:
    ① Can a logged-in user access another user's data by changing an ID?
    ② Can this loop, retry, or fire more than once? What does it cost if it does?
    ③ Can this be called by someone not logged in? Should it be?
    ④ What input breaks it — empty, null, huge, wrong type, negative, unicode?
    ⑤ What happens if the network fails halfway through?
    List concrete failures with the line. Do not summarise the code."

⭐ THEN BRING ITS FINDINGS BACK TO ME. Disagreement between the two models
  is the signal — that is where I look myself.
```

---

# 3 · Stack

**Default stack. If a project uses something else, keep every rule in this file and swap the code.**

| Layer | Default | Rule that survives a swap |
|---|---|---|
| Framework | Next.js (App Router) + TypeScript | Server-side is the only place authorization exists |
| UI | **shadcn/ui** as the base + **[reactbits.dev](https://reactbits.dev/)** for motion/effect pieces | Own the components; do not depend on a design you cannot edit |
| DB | Supabase (Postgres) | Row-level ownership filter on every query, server-side |
| Auth | Clerk | Never trust a client-supplied user id |
| Payments | Stripe | Verify webhook signatures; make every charge idempotent |
| Cache / limits | Upstash Redis | Every public endpoint is rate-limited before merge |
| Errors | Sentry | The error you never see is the one that loses the customer |
| Hosting | Vercel | Preview deploy per PR; rollback is one click and I have tested it |
| Edge | Cloudflare | WAF + bot protection on before launch |

**Money always: integers in the smallest unit. Never a float. Dates always: store UTC, format at render.**

---

# 4 · ⭐⭐ UI — modern, not "AI-generated"

```
⭐⭐ THE PROBLEM: DEFAULT AI OUTPUT ALL LOOKS THE SAME.
   Purple-to-blue gradient hero · three feature cards with emoji icons ·
   "Empower your workflow" · glassmorphism · centred everything ·
   a full-width testimonial nobody wrote.
   ⇒ ⭐ A REVIEWER RECOGNISES IT INSTANTLY, AND IT READS AS "NOBODY DECIDED
     ANYTHING." THAT IS THE THING TO AVOID.
```

**The rules:**

| ⭐ Do | ⭐⭐ Don't |
|---|---|
| **shadcn/ui as the base** — I own the code, I can edit it | Ship a component library's default theme untouched |
| **[reactbits.dev](https://reactbits.dev/) for the moments that need motion** — one hero effect, one reveal, one interactive detail | Put an animation on everything. Motion everywhere = motion nowhere |
| **One accent colour**, a real neutral ramp, and use it sparingly | Gradients as a substitute for a decision |
| **Pick a real typeface** and two weights | Default system font + `font-bold` everywhere |
| **Asymmetry, generous whitespace, one strong focal point** | Everything centred in a 1200px column |
| **Real content and real numbers** | "Lorem ipsum", "Feature One", stock avatars |
| **Copy a layout you admire, structurally** | Ask for "a modern landing page" and accept the first result |

```
⭐⭐ HOW TO PROMPT FOR UI SO IT DOES NOT LOOK GENERIC

  ❌ "Make a modern landing page for a SaaS product."
  ✅ "Build the hero using shadcn Button and Card as the base.
      Layout: asymmetric — headline and CTA left at 55% width, product
      screenshot right, bleeding off the right edge.
      One accent colour (#___), everything else neutral greys.
      No gradient background. No glassmorphism. No emoji icons —
      use lucide icons at 20px, stroke 1.5.
      Add ONE reactbits effect: <name> on the headline only.
      Real copy: '<my actual headline>'. No placeholder text anywhere."

⭐ SPECIFY: layout structure · what NOT to use · where the ONE effect goes ·
  real copy. ⭐⭐ THE "WHAT NOT TO USE" LINE IS THE ONE THAT CHANGES THE OUTPUT.
```

**Every screen needs four states — build all four or it is not done:**
**loading** (skeleton shaped like the content, not a spinner) · **error** (says what happened *and*
what to do, with a retry) · **empty** (split in two: *never had any* → the action that creates one;
*filtered to zero* → clear the filter) · **success**.

---

# 5 · ⭐⭐ Non-negotiables — customer data

```
⭐⭐ ASSUME EVERY REQUEST IS HAND-CRAFTED BY SOMEONE HOSTILE. THE BROWSER
   IS NOT A SECURITY BOUNDARY. THE SERVER IS.

 ① ⭐⭐ EVERY QUERY FILTERS BY THE AUTHENTICATED USER, SERVER-SIDE.
    Not "the UI only shows their rows". The QUERY.
    ⇒ Test: log in as user A, change an id in the URL to user B's,
      and confirm 404/403. ⭐ THIS IS THE #1 REAL LEAK.
 ② RLS ON in Supabase, and FORCE it. The app filter and RLS are two
    layers, not one — the second catches the query you forgot.
 ③ ⭐ NEVER `NEXT_PUBLIC_` ANYTHING SECRET. Anything in the bundle is
    public forever — view-source, and it is already in a CDN cache.
 ④ Never use the service_role key in client code. Ever.
 ⑤ ⭐ VALIDATE EVERY INPUT AT THE BOUNDARY (Zod). Never spread req.body
    into a DB call — whitelist the fields.
 ⑥ ⭐⭐ NEVER RETURN A WHOLE DB ROW. Return an explicit shape. A DTO is an
    allow-list; it protects the field someone adds next year.
 ⑦ Never log PII — no emails, tokens, addresses, card data — to logs or
    Sentry. Scrub before send.
 ⑧ Password reset, email change, and payment routes get rate limits and
    re-authentication.
 ⑨ ⭐ FILE UPLOADS: check magic bytes not the extension, cap the size,
    strip EXIF (photos carry GPS), serve from a separate origin.
 ⑩ ⭐⭐ ON EVERY DEPLOY: /.env and /.git/config must return 404.
```

**Before I say "customer data is secure", these four must be true and tested:**
authorization is enforced server-side and I have run the ID-swap test · RLS is on · no secret is in
the client bundle (`grep` the build output) · uploads are validated by content.

---

# 6 · ⭐⭐ Cost — the failure that hurts most

```
⭐⭐ THE MOST EXPENSIVE VIBE-CODING BUG IS NOT A LEAK. IT IS A LOOP.
   A useEffect with no dependency array, deployed Friday, 40 million
   requests by Monday. The page looks FINE the whole time.

EVERY CALL NEEDS THREE CEILINGS:
   ① a LOOP GUARD    — correct dependency array; no state set inside an
                       effect that re-triggers itself; MAX_STEPS on any
                       AI/agent loop
   ② a RETRY CAP     — max 3, exponential backoff + jitter, never retry
                       4xx except 429
   ③ a SPEND CAP     — a hard cap at the provider, and an alert BELOW it

AND: debounce search at 400ms/2 chars · cancel in-flight requests on
unmount · clear every setInterval · rate-limit every public endpoint
(per user AND per IP) · idempotency key on every payment.
```

---

# 7 · Traffic & scale — answer before launch, not after

| Question | The answer I want |
|---|---|
| **What happens at 10× traffic?** | Name what breaks *first* — usually the database, not the app |
| **What is the slowest query?** | Measured, with `EXPLAIN ANALYZE`. Every foreign key and every filtered column is indexed |
| **What is cached, and for how long?** | Static pages at the CDN; hot reads in Redis; a stated TTL per thing |
| **What is the connection limit?** | Serverless + Postgres = pooling required (pgBouncer / Supabase pooler), or you exhaust connections at modest traffic |
| **What is slow and can be async?** | Email, PDFs, image processing, AI calls → a queue, not the request |
| **What happens when a dependency is down?** | Timeout, fallback, and a user-visible message — never an infinite spinner |

**Scale in this order — do not skip to the last one:** index the query → cache the read → move slow
work off the request → add a read replica → shard. **Most "we need to scale" is one missing index.**

---

# 8 · Which AI tool for which job

| Job | Use |
|---|---|
| **Complex / risky change** | ⭐⭐ **Plan mode** — read the plan, correct it, approve it |
| Correctness bugs, simplification | `/code-review` |
| Auth, data, payments, uploads changed | `/security-review` |
| Tidy up quality without hunting bugs | `/simplify` |
| **Final check on money/auth/data** | ⭐⭐ **Codex second opinion** (§2) |
| Searching a big unfamiliar codebase | A search subagent — cheaper than loading everything |
| Set up repo rules for the agent | `/init` then edit — see `CLAUDE-md-template.md` |
| Long/repeating task | `/loop` |
| **Deciding architecture, debugging something subtle** | ⭐ The strongest model. Do not economise here |
| Mechanical edits across many files | A faster model is fine |

---

# 9 · ⭐⭐ Ship checklist — nothing goes live until every line is ticked

```
CONTENT
  □ ⭐ NO PLACEHOLDER TEXT ANYWHERE — no lorem ipsum, no "Feature One",
     no "Your Company", no stock avatar of a person who does not exist
  □ Real copy everywhere, spell-checked
  □ Every image has meaningful alt text

LAYOUT & MOBILE
  □ ⭐⭐ NO HORIZONTAL SCROLLING at any width. Test 320px.
     (Usual causes: a fixed width, a long unbroken string, an image with
      no max-width, a table not wrapped in overflow-x:auto, 100vw + padding)
  □ ⭐ NO MOBILE OVERFLOW — nothing clipped or cut off
  □ ⭐⭐ A WORKING MOBILE MENU — opens, closes, closes on navigate,
     traps focus, closes on Escape
  □ Tap targets ≥ 44px. Text ≥ 16px on inputs (or iOS zooms on focus)
  □ ⭐ TESTED ON A REAL PHONE, ON MOBILE DATA — not just devtools

STATES
  □ ⭐⭐ A REAL 404 PAGE — branded, with a route back
  □ ⭐ AN EMPTY STATE on every list — and split in two (never-had-any vs
     filtered-to-zero). "No results yet" shown to someone with a bad
     filter reads as "my data is gone"
  □ ⭐⭐ ERROR MESSAGES that say what happened and what to do next
  □ ⭐⭐ SUCCESS MESSAGES — every action confirms visibly. Silence reads
     as failure and people click twice
  □ Loading skeletons, not layout-shifting spinners
  □ A 500 page

SEO & META
  □ ⭐ UNIQUE PAGE TITLE PER PAGE — "Page — Site", never "React App"
  □ ⭐ META DESCRIPTION PER PAGE, 140–160 chars, written not generated
  □ ⭐⭐ FAVICON — full set, not the framework default
  □ Open Graph + Twitter card image (1200×630) — this is what shows in
     every share and message preview
  □ robots.txt, sitemap.xml, canonical URLs
  □ One h1 per page, headings in order

LINKS & CONTACT
  □ ⭐⭐ NO BROKEN LINKS — crawl the built site and prove it
  □ No link to a page that does not exist yet
  □ ⭐ EMAIL IS CLICKABLE — mailto:
  □ ⭐ PHONE IS CLICKABLE — tel: with the full international number
  □ External links: target="_blank" rel="noopener noreferrer"

PERFORMANCE
  □ ⭐⭐ IMAGES COMPRESSED AND SIZED — WebP/AVIF, srcset, width+height
     set (or the page jumps), lazy below the fold, EAGER on the hero
  □ Lighthouse ≥ 90 on mobile profile
  □ No render-blocking script; fonts with font-display: swap

SECURITY & MONEY
  □ ⭐⭐ ID-SWAP TEST PASSED — log in as A, try to read B's record
  □ /.env and /.git/config return 404
  □ No secret in the bundle (grep the build output)
  □ Rate limits live on every public endpoint
  □ Spend caps + alerts on every paid service

OPERATIONS
  □ Sentry live, and I have triggered a real error and seen it arrive
  □ Uptime check + alert that reaches my phone
  □ Analytics recording the one action the site exists for
  □ ⭐ ROLLBACK TESTED — not "possible", actually done once
  □ ⭐⭐ LEGAL — privacy policy, terms, cookie notice and refund
     policy exist, are linked, and DO NOT 404 on the live domain
  □ ⭐ No non-essential tracker fires before consent
  □ ⭐ "Delete my account" works and reaches every system
  □ ⭐⭐ npx license-checker — no AGPL/GPL in a commercial product
  □ ⭐ Every font and image has a licence you have read
  □ ⭐ Email: SPF, DKIM and DMARC set — or resets land in spam
```

> ⭐ **Depth:** [12-Legal-and-Compliance.md](12-Legal-and-Compliance.md) ·
> **If the product uses a model:** [13-AI-Features.md](13-AI-Features.md)

---

# 10 · Things you do that I need you to stop doing

```
⭐⭐ THESE ARE PATTERNS, NOT ACCIDENTS. GUARD AGAINST THEM.

 ① ⭐⭐ CLAIMING SOMETHING IS TESTED WHEN YOU DID NOT RUN IT.
    Say "I have not run this" if you have not.
 ② INVENTING A PACKAGE, AN API, OR A CONFIG OPTION THAT DOES NOT EXIST.
    ⇒ if you are not sure it exists, SAY SO and check.
 ③ ⭐ SILENTLY CHANGING FILES OUTSIDE THE TASK.
 ④ "FIXING" A FAILING TEST BY DELETING IT OR WEAKENING THE ASSERTION.
 ⑤ ⭐⭐ CATCHING AN ERROR AND SWALLOWING IT so the screen goes blank
    instead of showing what went wrong.
 ⑥ Adding a dependency for something the platform already does.
 ⑦ ⭐ REWRITING WORKING CODE because you would have written it
    differently.
 ⑧ Hard-coding a value that should be config — and worse, a secret.
 ⑨ ⭐⭐ AGREEING WITH ME WHEN I AM WRONG. If I ask for something that
    will leak data, cost money, or break, SAY SO FIRST.
```

---

# 11 · When I ask a vague question

| I say | You do |
|---|---|
| *"Make it look better"* | Ask what specifically feels wrong, or propose 2–3 concrete changes with reasons. Do not restyle everything |
| *"Make it faster"* | Measure first. Tell me the numbers, then propose one fix |
| *"Make it secure"* | Run §5 as an audit and report findings — do not add random headers |
| *"Make it scale"* | Ask what traffic, then §7. Usually the answer is one index |
| *"It's broken"* | Ask for the error text and what I did. Do not guess and rewrite |
| *"Add tests"* | Ask what matters. Test the money path and the failure path, not getters |

---

**Depth on any line above:** [the folder index](README.md) · **Per-project rules file:**
[CLAUDE-md-template.md](CLAUDE-md-template.md) · **The full audit:**
[10-Ship-Checklist.md](10-Ship-Checklist.md)
