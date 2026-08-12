# Phase 03 — Architecture & Data

The schema is the hardest thing to change later. Get it right before the agent generates 40 files
that depend on it.

**Gate to pass this phase:** ERD final, migrations committed, **RLS policies written and tested with
a second account** before any feature uses the table.

---

## 1. Schema rules

- [ ] ERD finalised before any table is created
- [ ] Foreign keys with **explicit** cascade / restrict rules — decide, don't inherit a default
- [ ] Constraints at the **database** level, not just the app (unique, not-null, check)
- [ ] Every table has `created_at` and `updated_at`
- [ ] All timestamps `timestamptz`, stored UTC, converted only at display
- [ ] Soft delete vs hard delete decided **per table** and written down
- [ ] **UUIDs, not sequential IDs, for anything in a URL** — sequential IDs leak your volume and
      invite enumeration
- [ ] Every user-owned table has a `user_id` column with an index
- [ ] Multi-tenancy: `tenant_id` on every row, enforced at the DB, if applicable
- [ ] Money as `numeric`, never `float`
- [ ] Enums as Postgres enums or a check constraint, not free text

```sql
create table notes (
  id          uuid primary key default gen_random_uuid(),
  user_id     uuid not null references auth.users(id) on delete cascade,
  title       text not null check (length(title) between 1 and 200),
  body        text not null default '',
  created_at  timestamptz not null default now(),
  updated_at  timestamptz not null default now()
);
create index notes_user_id_idx on notes(user_id);
create index notes_user_created_idx on notes(user_id, created_at desc);  -- for the list query
```

---

## 2. RLS — write it now, not later

**A Supabase table without RLS is public.** The anon key ships in your browser bundle, so anyone who
opens devtools can query any table that has RLS disabled.

Every row of your [auth matrix](01-Scope-and-Planning.md#auth-matrix--the-format) becomes a policy.

```sql
alter table notes enable row level security;

create policy "read own"   on notes for select
  using (auth.uid() = user_id);

create policy "insert own" on notes for insert
  with check (auth.uid() = user_id);

create policy "update own" on notes for update
  using (auth.uid() = user_id) with check (auth.uid() = user_id);

create policy "delete own" on notes for delete
  using (auth.uid() = user_id);
```

- [ ] RLS enabled on **every** table — including join tables and lookup tables
- [ ] `with check` on insert and update, not just `using` — `using` alone lets a user
      *reassign a row to themselves* or *away from themselves*
- [ ] Policies tested with a **second real account**, not assumed
- [ ] `service_role` used only in trusted server code — it bypasses RLS completely
- [ ] Storage buckets have their own policies — they are not covered by table RLS

### With Clerk as the identity provider

If Clerk holds identity and Supabase holds data, `auth.uid()` is empty unless you bridge them.
Pick one:

1. **Clerk JWT template** issuing a Supabase-compatible token, so `auth.jwt()->>'sub'` is the Clerk
   user ID — then write policies against that claim.
2. **Server-side only access**: all queries go through your Next.js server using the service role,
   and *you* apply the `user_id` filter in every query.

Option 2 is simpler to reason about but means **one forgotten filter is a full data leak** — the
database is no longer backing you up. If you choose it, the query-level ownership filter in
[Phase 06](06-Security.md#2-authorization--the-idor-layer) becomes mandatory, not advisory.

---

## 3. Migrations

- [ ] Versioned, reversible, committed to git
- [ ] **Read every generated migration before running it** — agents write destructive ones
      (dropping a column instead of renaming is the classic)
- [ ] Run against dev, then staging, then prod — never straight to prod
- [ ] Backfills separated from schema changes
- [ ] Never `drop column` in the same deploy that stops using it — two deploys, so you can roll back
- [ ] Seed data script for local dev

---

## 4. Query patterns to establish now

```ts
// ✅ Ownership in the query — the anti-IDOR pattern
const note = await db
  .from('notes')
  .select('id, title, body, created_at')     // named columns, not *
  .eq('id', noteId)
  .eq('user_id', userId)                      // ← the line that matters
  .single();

// ✅ Bounded list — server enforces the ceiling regardless of what the client asks
const limit = Math.min(Number(searchParams.get('limit') ?? 50), 100);
const { data } = await db
  .from('notes')
  .select('id, title, created_at')
  .eq('user_id', userId)
  .order('created_at', { ascending: false })
  .limit(limit);
```

- [ ] Named columns, never `select('*')` — `*` leaks columns you add later
- [ ] Every list query has a **server-enforced** maximum
- [ ] Cursor pagination for anything that could exceed a few thousand rows (`OFFSET` degrades badly)
- [ ] Indexes on every FK and every column used in `WHERE` / `ORDER BY`
- [ ] N+1 avoided — use a join / nested select, not a loop

---

## 5. Storage

- [ ] Files in **Supabase Storage or Cloudflare R2** — never in a table column
- [ ] Bucket policies mirror table policies
- [ ] Uploads: type allowlist, size cap, sanitised filename
- [ ] Signed URLs with short expiry for private files
- [ ] User-uploaded content served from a **separate origin** — a stored HTML/SVG file on your main
      domain is stored XSS with access to your cookies
- [ ] R2 over S3 if egress is significant (no egress fees)

---

## 6. Background work

Anything slower than ~2 seconds does not belong in a request.

- [ ] Queue chosen (Upstash QStash / Supabase Edge Functions + cron / Inngest)
- [ ] Jobs **idempotent** — they will be retried
- [ ] Retries bounded, with a dead-letter path
- [ ] Job status visible to the user (a job ID they can poll, with a sane interval)
- [ ] Long jobs report progress rather than appearing frozen

---

## 7. Connection pooling

Serverless functions exhaust Postgres connections long before CPU becomes an issue. Each invocation
opens its own connection; 100 concurrent requests means 100 connections against a limit of ~60.

- [ ] **Supabase pooler used (port 6543), not the direct connection (5432)**, from serverless
- [ ] Direct connection reserved for migrations
- [ ] Connection limit understood for your plan tier

This is not an optimisation. Without it the app works in development and fails under the first real
traffic spike, with an error that looks nothing like its cause.

---

## Phase gate

- [ ] ERD final and committed
- [ ] RLS enabled on every table, `with check` on writes
- [ ] Policies verified with a second account
- [ ] Indexes on FKs and filter columns
- [ ] Pooler connection string in use
- [ ] Migrations reversible and reviewed by eye
