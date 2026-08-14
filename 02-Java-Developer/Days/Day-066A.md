# Day 066A ➕

**L-066A** · `ThreadLocal` — how Spring knows "the current user" · **the thread-pool memory leak**

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** There are three ways to make code thread-safe: don't share, make it
> immutable, or synchronize. The roadmap covers the second and third thoroughly and never names the
> first. That leaves two holes. **Mechanism:** `SecurityContextHolder`, `TransactionSynchronizationManager`,
> `RequestContextHolder` and MDC logging are all `ThreadLocal`, so Stage 4 and Stage 6 would be
> memorised rather than understood. **Leak:** it is one of the seven leak patterns from Day 027, and
> it only manifests under a thread pool — which means it never shows up in testing.

---

# Part 1 — L-066A · Thread confinement

## The idea

If two threads never touch the same data, no synchronization is needed. That is **thread
confinement**, and it is the cheapest correctness strategy that exists because it has no runtime
cost at all.

You already rely on it: local variables live on the thread's own stack (Day 025), so they are
confined by construction. `ThreadLocal` extends the same property to something that *looks* like a
shared static field but isn't.

```java
public class Confined {
    private static final ThreadLocal<SimpleDateFormat> FORMAT =
            ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

    public static String format(Date d) {
        return FORMAT.get().format(d);        // each thread gets its OWN formatter
    }
}
```

`SimpleDateFormat` is the classic example because it is **mutable and not thread-safe** — it keeps
parsing state in a field, so two threads sharing one produce silently wrong dates, not an exception.
Historically the options were: create a new one per call (allocation churn), synchronize (contention
on a hot path), or confine one per thread. `ThreadLocal` was the third.

*(Since Java 8 the right answer is `DateTimeFormatter`, which is immutable and thread-safe — Day
042A. Keep the example anyway; it is what the pattern was built for and it still appears in
interviews.)*

## How it actually works — and this is the part that matters

The naive mental model is "a map from Thread to value inside the ThreadLocal object". **It is the
opposite**, and the inversion is the reason the leak exists.

```java
class Thread {
    ThreadLocal.ThreadLocalMap threadLocals;    // ← the map lives on the THREAD
}

class ThreadLocalMap {
    static class Entry extends WeakReference<ThreadLocal<?>> {
        Object value;                            // ← the VALUE is a strong reference
    }
    Entry[] table;
}
```

Read the two annotated lines together, because everything follows from them:

- The map is a field **on each `Thread` object**, keyed by the `ThreadLocal` instance.
- `get()` is therefore: fetch the current thread, look in its own map. **No shared state, no
  locking, ~10 ns.** That is why it is fast.
- The map's `Entry` is a **weak** reference to the *key*, so an unreachable `ThreadLocal` can be
  collected.
- **But the value is a strong reference.**

```
   Thread (alive in a pool, for hours)
     └─ threadLocals: ThreadLocalMap
          └─ Entry
               ├─ key   ── weak ──►  ThreadLocal instance   (collectable)
               └─ value ── STRONG ─► your object            (NOT collectable)
```

## The leak

```java
// A servlet filter, or any pooled-thread handler
public void doFilter(Request req, Response res, Chain chain) {
    UserContext.set(loadUser(req));        // ThreadLocal.set(...)
    chain.doFilter(req, res);
    // ← forgot to remove()
}
```

Now trace it. The request ends. The **thread returns to the pool and stays alive** — that is the
entire point of a pool. Its `threadLocals` map still holds a strong reference to that user object,
which references a session, which references a cache, which references… megabytes.

Multiply by 200 pool threads. Nothing is ever collected because the GC roots are the live threads
themselves (Day 026's reachability). Heap usage climbs across days, `OutOfMemoryError` arrives, and
a heap dump shows the retained set rooted at `Thread` — which is exactly the signature to recognise
in Day 076's heap-dump work.

Worse in a servlet container: if the value's class was loaded by the **webapp's** class loader, that
class loader can never be collected either, so **redeploying the app leaks its entire class
space**. This is why Tomcat logs `"The web application created a ThreadLocal but failed to remove
it"` on shutdown — a message you will now recognise instantly.

And there is a correctness bug hiding next to the leak: the next request handled by that thread sees
**the previous request's user**. A stale `ThreadLocal` in an authentication filter is an
authorization bug, not just a memory one.

## The fix, and it is non-negotiable

```java
public void doFilter(Request req, Response res, Chain chain) {
    UserContext.set(loadUser(req));
    try {
        chain.doFilter(req, res);
    } finally {
        UserContext.clear();               // ThreadLocal.remove() — ALWAYS in finally
    }
}
```

> **Every `ThreadLocal.set()` on a pooled thread must be paired with a `remove()` in a `finally`.**

Note that `set(null)` is **not** equivalent — it leaves the entry in the map with a null value.
`remove()` deletes the entry. (`ThreadLocalMap` does opportunistically clean entries whose weak key
has been cleared, during `get`/`set`/`resize` — but only sometimes, and never for the value of a
still-referenced `ThreadLocal`, which is the usual case since yours is a `static final` field.)

## Code to type

```java
import java.util.concurrent.*;

public class ThreadLocalDemo {

    // 1. The idiom
    static final ThreadLocal<StringBuilder> BUF =
            ThreadLocal.withInitial(StringBuilder::new);

    // 2. A deliberately fat value, to make the leak visible
    static final ThreadLocal<byte[]> FAT = new ThreadLocal<>();

    public static void main(String[] args) throws Exception {
        // --- confinement is real ---
        ExecutorService pool = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 6; i++) {
            int n = i;
            pool.submit(() -> {
                BUF.get().append(n).append(' ');
                System.out.printf("%s -> %s%n",
                        Thread.currentThread().getName(), BUF.get());
            });
        }
        Thread.sleep(500);

        // --- the leak ---
        System.out.println("\nleaking 4 MB per task on a 4-thread pool:");
        ExecutorService leaky = Executors.newFixedThreadPool(4);
        for (int i = 0; i < 20; i++) {
            leaky.submit(() -> {
                FAT.set(new byte[4 * 1024 * 1024]);
                // no remove()
            });
        }
        Thread.sleep(1000);
        System.gc(); Thread.sleep(300);
        System.out.printf("used after gc: %,d MB%n", usedMb());   // ~16 MB retained

        // --- with remove() ---
        for (int i = 0; i < 20; i++) {
            leaky.submit(() -> {
                try { FAT.set(new byte[4 * 1024 * 1024]); }
                finally { FAT.remove(); }
            });
        }
        Thread.sleep(1000);
        System.gc(); Thread.sleep(300);
        System.out.printf("used after gc: %,d MB%n", usedMb());   // back down

        pool.shutdown(); leaky.shutdown();
    }

    static long usedMb() {
        Runtime r = Runtime.getRuntime();
        return (r.totalMemory() - r.freeMemory()) / (1024 * 1024);
    }
}
```

The first loop shows the point of confinement: **six tasks, three threads, and each thread's
`StringBuilder` accumulates only its own values** — `pool-1-thread-1 -> 0 3`, not `0 1 2 3 4 5`.
No locks anywhere.

The second and third loops show the leak and the fix. Run with `-Xmx256m` and no `remove()` and you
can push it to an `OutOfMemoryError`.

## Where you will meet it in real frameworks

| Framework | `ThreadLocal` holds | Why |
|---|---|---|
| Spring Security | `SecurityContextHolder` → the authenticated principal | So any method can ask "who is the current user?" without threading it through 12 parameters |
| Spring TX | `TransactionSynchronizationManager` → the current `Connection` | So `@Transactional` methods share one connection without passing it |
| Spring MVC | `RequestContextHolder` → the current request | `@RequestScope` beans |
| SLF4J / Logback | `MDC` → request id, user id | Every log line carries the correlation id — Day 073A |
| Hibernate | session-per-thread | The classic "open session in view" pattern |

**This table is the reason the day exists.** When Day 137 explains `@Transactional`, the question
"how does the proxy know which `Connection` this method should use?" has an answer you already know:
the connection is bound to the thread, and the proxy reads it from there. Combine that with Day
056A's proxy mechanics and Spring stops being magic.

It also explains a modern gotcha: **`ThreadLocal` context is lost when work moves to another
thread.** Submit a task to an executor from inside a request and the new thread has an empty
`SecurityContextHolder`. Same for `CompletableFuture.supplyAsync` (Day 069) and reactive pipelines.
Spring provides `DelegatingSecurityContextExecutor` for exactly this; reactive stacks abandoned
`ThreadLocal` entirely and use the `Context` in the subscription instead.

## `InheritableThreadLocal` — and why it is a trap in pools

```java
static final InheritableThreadLocal<String> TRACE = new InheritableThreadLocal<>();
```

A **newly created** child thread copies the parent's inheritable values at construction. Useful for
`new Thread(...)`; **useless and dangerous with a pool**, because pool threads are created once, at
some arbitrary earlier moment, and inherit whatever context happened to exist then — which is
usually the wrong request, forever.

Java 21's answer is `ScopedValue` (preview): immutable, explicitly scoped, automatically inherited
by structured-concurrency child tasks, and impossible to leak because the binding ends with the
block.

```java
private static final ScopedValue<User> CURRENT = ScopedValue.newInstance();

ScopedValue.where(CURRENT, user).run(() -> handle(request));   // unbound automatically
```

Mention this in an interview if virtual threads come up (Day 072) — it signals you have followed the
platform's direction, not just its history.

## When *not* to use it

`ThreadLocal` is invisible dependency injection. It makes the data flow implicit, which is exactly
what makes it convenient and exactly what makes it hard to test and reason about.

- **Prefer a parameter.** If a method needs the user, the honest design is a parameter.
- Use `ThreadLocal` for genuinely cross-cutting context that would otherwise thread through every
  signature: correlation ids, the security principal, the current transaction.
- Never use it as a general-purpose cache. It multiplies memory by thread count.
- Never use it to pass data between layers of your own code as a shortcut.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| `set()` without `remove()` on a pooled thread | Memory leak, and stale data leaking across requests |
| `set(null)` instead of `remove()` | Entry stays in the map |
| Assuming the map lives inside the `ThreadLocal` | You will not understand the leak |
| `InheritableThreadLocal` with a pool | Inherits stale context permanently |
| Expecting context to follow an async hand-off | Empty context on the executor thread |
| Using it instead of a parameter | Hidden coupling; hard to test |
| Storing webapp-classloader objects | Redeploy leaks the whole class loader |

---

## Interview questions

**Q: What is `ThreadLocal` and when do you use it?**
Per-thread storage that provides thread safety by confinement rather than locking — no shared state,
so no synchronization. Use it for cross-cutting context (security principal, transaction, request
id) and for expensive non-thread-safe objects.

**Q: Where is the value actually stored?**
In a `ThreadLocalMap` field on the `Thread` object, keyed by the `ThreadLocal` instance — not inside
the `ThreadLocal`. That is why lookup is lock-free and why a live thread retains the value.

**Q: How does `ThreadLocal` leak memory?**
The map entry holds a weak reference to the key but a **strong** reference to the value. A pooled
thread lives indefinitely, so without `remove()` the value is retained by a live GC root. In a
servlet container it can retain the webapp class loader too.

**Q: How does Spring know the current user?**
`SecurityContextHolder` keeps the `SecurityContext` in a `ThreadLocal`, populated by a filter at the
start of the request and cleared in a `finally` at the end.

**Q: What happens to `ThreadLocal` context when you submit to an executor?**
It does not propagate — the pool thread has its own map. You must copy it across explicitly, which
is what Spring's context-propagating executor wrappers do.

**Q: `ThreadLocal` with virtual threads?**
It works, but a million virtual threads each with their own map is a memory problem, and virtual
threads are not pooled so the leak pattern changes. `ScopedValue` is the intended replacement.

---

## Mini task

1. Run `ThreadLocalDemo`. Confirm each pool thread's `StringBuilder` holds only its own values.
2. Run the leak section with `-Xmx256m` and no `remove()` until you get an `OutOfMemoryError`.
3. Take a heap dump at that point (`jcmd <pid> GC.heap_dump leak.hprof`) and confirm the retained
   set is rooted at `Thread`.
4. Write a two-filter chain: filter A sets a `ThreadLocal` request id, filter B logs it. Run 10
   requests on a 2-thread pool without `remove()` and show a request seeing the wrong id.
5. Submit a task to an executor from inside a thread that has a `ThreadLocal` set, and confirm the
   pool thread sees nothing. Then write a wrapper `Runnable` that copies the value across.
6. Replace the `SimpleDateFormat` example with `DateTimeFormatter` and note that the `ThreadLocal`
   is no longer needed at all.

---

# 🚪 Exit questions

1. What are the three strategies for thread safety, and which one is `ThreadLocal`?
2. Where does the map live, and why does that make `get()` lock-free?
3. Which reference in the entry is weak, which is strong, and why does that matter?
4. Explain the leak in four sentences, including why a pool is required for it to appear.
5. Why is `set(null)` not a fix?
6. What non-memory bug accompanies the leak?
7. Name four framework features built on `ThreadLocal`.
8. Why does `@Transactional` need one?
9. Why is `InheritableThreadLocal` wrong for a thread pool?
10. What replaces it in Java 21, and what problem does that solve?
11. When should you use a parameter instead?

## 🎙️ Articulation drill

Two minutes: **"How does Spring Security know who the current user is, and what can go wrong?"**

The answer is one sentence of mechanism — a filter puts the principal in a `ThreadLocal` — followed
by the two failure modes: the leak on a pooled thread without `remove()`, and the loss of context
across an async hand-off. Being able to name both failure modes unprompted is what makes this
sound like production experience rather than documentation.

---

**Previous:** [Day 066](Day-066.md) · **Next:** [Day 067](Day-067.md) — `java.util.concurrent` locks

> Back to the main line. `synchronized` is the primitive; tomorrow is the toolkit Doug Lea built on
> top of it — and everything it can do that the keyword cannot.
