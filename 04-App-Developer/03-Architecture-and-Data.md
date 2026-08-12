# Phase 03 — Architecture & Data

**Gate to pass this phase:** ERD final, RLS tested with a second account, offline sync strategy
implemented and idempotent.

---

## 1. Schema rules

- [ ] ERD finalised before any table is created
- [ ] Foreign keys with explicit cascade / restrict rules
- [ ] Constraints at the **database** level (unique, not-null, check)
- [ ] `created_at` and `updated_at` on every table
- [ ] All timestamps `timestamptz`, UTC, converted only at display
- [ ] **UUIDs for anything a client references** — sequential IDs invite enumeration
- [ ] `user_id` column + index on every user-owned table
- [ ] Money as `numeric`, never `float`
- [ ] Soft vs hard delete decided per table

### Mobile-specific schema needs

- [ ] **`updated_at` indexed** — delta sync depends on "give me everything changed since X"
- [ ] A `deleted_at` column where clients need to know something was deleted (a hard delete is
      invisible to an offline client, which keeps showing the row forever)
- [ ] A server-generated `version` or `revision` per row if you need conflict detection
- [ ] Client-generated UUIDs accepted for creates, so offline writes have an ID before they sync

```sql
create table notes (
  id          uuid primary key,                    -- client may generate this offline
  user_id     uuid not null references auth.users(id) on delete cascade,
  title       text not null check (length(title) between 1 and 200),
  body        text not null default '',
  deleted_at  timestamptz,                         -- soft delete, so clients can sync deletions
  created_at  timestamptz not null default now(),
  updated_at  timestamptz not null default now()
);
create index notes_user_id_idx    on notes(user_id);
create index notes_user_sync_idx  on notes(user_id, updated_at desc);   -- delta sync
```

---

## 2. RLS — mandatory here

**Your Supabase anon key is compiled into an app binary distributed to every user's device.**
It is public, permanently. RLS is the only thing between it and your data.

```sql
alter table notes enable row level security;

create policy "read own"   on notes for select using (auth.uid() = user_id);
create policy "insert own" on notes for insert with check (auth.uid() = user_id);
create policy "update own" on notes for update
  using (auth.uid() = user_id) with check (auth.uid() = user_id);
create policy "delete own" on notes for delete using (auth.uid() = user_id);
```

- [ ] RLS on **every** table, including join and lookup tables
- [ ] `with check` on insert and update, not just `using`
- [ ] Tested with a **second real account**
- [ ] `service_role` never in the app — server-side only
- [ ] Storage buckets have their own policies

### With Clerk

`auth.uid()` is empty unless you bridge Clerk to Supabase. Either use a Clerk JWT template issuing
a Supabase-compatible token (policies read `auth.jwt()->>'sub'`), or route all data access through
your own backend. On mobile, prefer the JWT template — a backend hop adds latency on a connection
that's already slow.

---

## 3. Offline architecture

The part web apps don't have.

### Layers

```
UI  →  TanStack Query (memory + persisted cache)  →  SQLite/MMKV  →  API  →  Supabase
                              ↑
                     mutation queue (replayed on reconnect)
```

- [ ] Query cache persisted to disk so the app opens with content, not a spinner
- [ ] Network status detected (`@react-native-community/netinfo`) and surfaced
- [ ] Reads served from cache when offline
- [ ] Writes queued and replayed on reconnect
- [ ] **Queued writes idempotent** — replay must not double-create. Client-generated UUIDs make
      this straightforward.
- [ ] Conflict resolution decided and written down
- [ ] Optimistic UI rolls back **visibly** on failure — a silent rollback looks like data loss
- [ ] Queue bounded — don't accumulate 10,000 pending writes forever
- [ ] Sync failures surfaced after N attempts, not retried silently forever

```ts
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 2,
      retryDelay: a => Math.min(1000 * 2 ** a, 10_000),
      staleTime: 60_000,
      gcTime: 24 * 60 * 60 * 1000,        // keep cache across app restarts
      refetchOnWindowFocus: false,         // on mobile this fires constantly
      networkMode: 'offlineFirst',
    },
    mutations: { networkMode: 'offlineFirst', retry: 3 },
  },
});
```

### Delta sync

Full re-downloads burn the user's data plan and battery.

```ts
// Only what changed since last sync
const { data } = await supabase
  .from('notes')
  .select('*')
  .eq('user_id', userId)
  .gt('updated_at', lastSyncedAt)
  .order('updated_at', { ascending: true })
  .limit(500);                              // bounded, paginate through
```

- [ ] `lastSyncedAt` stored locally and updated only after a successful full page
- [ ] Deletions synced via `deleted_at`, not by absence
- [ ] Sync bounded and paginated
- [ ] Initial sync shows progress rather than appearing frozen

---

## 4. State restoration

The OS kills backgrounded apps freely. Users don't perceive that as "the app restarted".

- [ ] Navigation state restored after an OS kill
- [ ] In-progress form input preserved (drafts to MMKV/SQLite)
- [ ] Deep link into a killed app lands on the right screen **with a sensible back stack**
- [ ] Auth session survives restart (SecureStore)
- [ ] Tested: background the app, force-kill it, reopen

---

## 5. Query patterns

```ts
// ✅ Ownership in the query — the anti-IDOR pattern
const { data } = await supabase
  .from('notes')
  .select('id, title, body, updated_at')     // named columns, not *
  .eq('id', noteId)
  .eq('user_id', userId)                      // ← the line that matters
  .single();

// ✅ Bounded list
const limit = Math.min(requested ?? 50, 100);
```

- [ ] Named columns, never `select('*')`
- [ ] **Server-enforced** maximum page size
- [ ] Cursor pagination for long lists
- [ ] Indexes on FKs and sort columns
- [ ] N+1 avoided — nested select, not a loop

---

## 6. Storage & media

- [ ] Files in Supabase Storage or **Cloudflare R2** (no egress fees — phones download a lot of images)
- [ ] Images resized **server-side**, multiple sizes stored; never send a 4000px image to a 100px thumbnail
- [ ] Uploads: type allowlist, size cap, sanitised filename
- [ ] Signed URLs with short expiry for private files
- [ ] Upload resumable or retryable — mobile uploads fail mid-transfer routinely
- [ ] Local image cache bounded (`expo-image` handles this)

---

## 7. Background work

- [ ] Slow work off the request path — queue it, return a job ID
- [ ] Background sync uses the OS scheduler (`expo-background-task` → WorkManager / BGTaskScheduler),
      **never** a `setInterval`
- [ ] Background work respects battery and Doze mode; the OS will throttle you regardless
- [ ] Jobs idempotent, retries bounded, dead-letter path

---

## Phase gate

- [ ] ERD final; `updated_at` indexed for delta sync
- [ ] RLS on every table, verified with a second account
- [ ] Offline mode implemented per the Phase 01 decisions
- [ ] Queued writes idempotent, tested by replaying them
- [ ] State restoration tested after a force-kill
- [ ] Delta sync bounded and paginated
