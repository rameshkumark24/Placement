# Day 056A ➕

**Reflection & custom annotations** — the prerequisite that makes Spring stop being magic

**Time:** 3 hrs · **Mode:** NEW

> **This is an added day, and it's a prerequisite the roadmap was missing.** Day 137 (AOP), Day 140
> (auto-configuration internals) and Day 174 (`@PreAuthorize`) all assume you understand reflection.
> Without it, Stage 4 teaches you Spring's *behaviour* rather than its *mechanism* — which defeats the
> point of doing Stage 3 before Stage 4.
>
> See [Gap-Audit.md](../Roadmap/Gap-Audit.md#3-reflection--custom-annotations--day-056a).

---

# Reflection

## What it is

**The ability to inspect and manipulate classes, methods and fields at runtime, by name.**

Ordinary Java is resolved at compile time — you write `user.getName()` and the compiler emits an
`invokevirtual` to a known method (Day 024). Reflection defers all of that to runtime:

```java
Class<?> clazz = Class.forName("com.app.User");
Object instance = clazz.getDeclaredConstructor().newInstance();
Method m = clazz.getMethod("getName");
Object result = m.invoke(instance);
```

**Nothing in that code names `User` at compile time.** That's the whole capability — and it's what
every framework you use is built on.

## The `Class` object

**Every loaded type has exactly one `Class` object**, created by the class loader (Day 023) and living
in Metaspace (Day 025).

```java
Class<?> c1 = String.class;              // class literal — compile-time
Class<?> c2 = "hello".getClass();        // from an instance
Class<?> c3 = Class.forName("java.lang.String");   // by name — TRIGGERS class loading
```

**`Class.forName` initialises the class** (runs static blocks — Day 033). That's how the old JDBC
driver-registration idiom worked:

```java
Class.forName("com.mysql.cj.jdbc.Driver");   // the static block registers the driver
```

## The core API

```java
Class<?> c = User.class;

// Structure
c.getName();                 // com.app.User
c.getSimpleName();           // User
c.getSuperclass();
c.getInterfaces();
c.getModifiers();            // use Modifier.isPublic(...) etc.

// Members — getX() = public, including inherited
//           getDeclaredX() = ALL access levels, this class only
c.getFields();               // public fields, incl. inherited
c.getDeclaredFields();       // all fields declared here, any access
c.getMethods();
c.getDeclaredMethods();
c.getDeclaredConstructors();

// Generic signatures — these SURVIVE erasure (Day 055)
field.getGenericType();      // java.util.List<com.app.Order>
```

**The `get` versus `getDeclared` distinction is the one people trip on.** `getFields()` returns public
fields including inherited ones; `getDeclaredFields()` returns all fields declared in this class
regardless of access, but not inherited ones. To get everything you walk the hierarchy yourself.

## Breaking encapsulation

```java
Field f = User.class.getDeclaredField("password");
f.setAccessible(true);                          // ← bypass private
String value = (String) f.get(userInstance);
f.set(userInstance, "changed");
```

**`setAccessible(true)` defeats `private`.** That's how Hibernate populates entity fields without
setters, how Jackson deserialises into private fields, and how Spring injects into
`@Autowired private` fields.

**Since Java 9 (JPMS — Day 036) this is restricted across module boundaries** unless the package is
`opens`ed. `InaccessibleObjectException` and the "illegal reflective access" warnings you may have
seen come from exactly this.

**`setAccessible` also defeats `final`** on instance fields — which is one reason Day 034 noted
`final` isn't a security boundary.

## Instantiation and invocation

```java
// Constructor
Constructor<User> ctor = User.class.getDeclaredConstructor(String.class, int.class);
User u = ctor.newInstance("Ramesh", 25);

// No-arg — why JPA entities need one (Day 035)
User u2 = User.class.getDeclaredConstructor().newInstance();

// Method
Method m = User.class.getDeclaredMethod("setName", String.class);
m.setAccessible(true);
m.invoke(u, "New Name");

// Static method — null receiver
Method sm = Math.class.getMethod("max", int.class, int.class);
int max = (int) sm.invoke(null, 3, 7);
```

**This is precisely why Hibernate requires a no-arg constructor** — it calls
`getDeclaredConstructor().newInstance()` and then populates fields reflectively. Day 035's point,
now with the mechanism.

## The cost

**Reflection is slower than direct calls**, though far less than folklore suggests on a modern JVM.

| | Relative cost |
|---|---|
| Direct call | 1× |
| Reflective call, cached `Method` | ~2–3× |
| Reflective call, `getMethod` each time | ~20×+ |
| `setAccessible(true)` each time | Adds a security check |

**The dominant cost is *lookup*, not *invocation*.** So the rule is: **look up once, cache the
`Method`/`Field`, reuse it.** That's exactly what Spring and Jackson do at startup — which is why
Spring Boot's startup is slow and its steady-state throughput isn't.

**Other costs:** no compile-time checking (a renamed method fails at runtime), the JIT can't inline
through most reflective calls, and stack traces get noisy.

---

# Annotations

## What an annotation is

**Metadata attached to code, readable at compile time or runtime.** It does nothing by itself —
**something must read it and act.**

That last sentence is the key insight. `@Override` is read by the compiler. `@Test` is read by JUnit.
`@Autowired` is read by Spring. **The annotation is inert; the processor is where the behaviour
lives.**

## Writing one

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)      // ← available via reflection
@Target(ElementType.METHOD)              // ← where it may be applied
@Documented
public @interface Timed {
    String value() default "";           // elements look like methods
    boolean logArgs() default false;
}
```

```java
@Timed(value = "expensive-operation", logArgs = true)
public void doWork(String input) { ... }
```

### `@Retention` — the one that matters most

| Policy | Kept until | Example |
|---|---|---|
| `SOURCE` | Discarded by the compiler | `@Override`, `@SuppressWarnings`, Lombok |
| `CLASS` | In the class file, **not** loaded at runtime (default) | Bytecode tools |
| **`RUNTIME`** | **Readable by reflection** | `@Test`, `@Autowired`, `@Entity` |

**If you want to read an annotation reflectively it MUST be `RUNTIME`.** Forgetting this is the
single most common mistake when writing a first custom annotation — it silently does nothing.

### `@Target`

```java
@Target({ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
```

`TYPE`, `METHOD`, `FIELD`, `PARAMETER`, `CONSTRUCTOR`, `LOCAL_VARIABLE`, `ANNOTATION_TYPE`,
`PACKAGE`, `TYPE_PARAMETER`, `TYPE_USE`.

**Omit `@Target` and it applies anywhere** — usually not what you want.

### `@Inherited` and `@Repeatable`

```java
@Inherited      // subclasses inherit it — CLASS annotations only, not methods or fields
@Repeatable(Schedules.class)    // may appear more than once on the same element
```

**`@Inherited` only works on classes**, which surprises people expecting method annotations to be
inherited by overrides. They aren't — Spring works around this with its own
`AnnotatedElementUtils`.

## Reading them

```java
if (method.isAnnotationPresent(Timed.class)) {
    Timed t = method.getAnnotation(Timed.class);
    String name = t.value();
    boolean logArgs = t.logArgs();
}

for (Annotation a : method.getAnnotations()) { ... }
```

## Putting it together — a mini framework

**This is the exercise that makes Spring click.** Reflection plus annotations plus a dynamic proxy is
*all* a framework is.

```java
// A trivial @Test runner — JUnit in 15 lines
static void runTests(Class<?> testClass) throws Exception {
    Object instance = testClass.getDeclaredConstructor().newInstance();
    for (Method m : testClass.getDeclaredMethods()) {
        if (m.isAnnotationPresent(Test.class)) {
            m.setAccessible(true);
            try { m.invoke(instance); System.out.println("PASS " + m.getName()); }
            catch (InvocationTargetException e) {
                System.out.println("FAIL " + m.getName() + ": " + e.getCause());
            }
        }
    }
}
```

**That is JUnit's core.** Scan for the annotation, instantiate, invoke, catch. Everything else is
reporting, lifecycle and assertions.

## Dynamic proxies — how Spring AOP actually works

```java
Object proxy = Proxy.newProxyInstance(
    target.getClass().getClassLoader(),
    target.getClass().getInterfaces(),
    (p, method, args) -> {
        long start = System.nanoTime();
        try { return method.invoke(target, args); }        // delegate
        finally {
            System.out.printf("%s took %d µs%n", method.getName(),
                    (System.nanoTime() - start) / 1000);
        }
    });
```

**A JDK dynamic proxy implements a set of interfaces and routes every call through your
`InvocationHandler`.** That's `@Transactional`, `@Cacheable`, `@Async` and `@PreAuthorize` — the proxy
does the work before and after delegating.

**And now Day 137's puzzle answers itself:**

> **Why does `@Transactional` silently fail on self-invocation?**
>
> Because the proxy wraps the object. An external call goes proxy → handler → target. But an internal
> `this.method()` call goes **straight to the target**, bypassing the proxy entirely — so the handler
> never runs, and no transaction is started.

**You now understand that before you meet it**, which was the point of adding this day.

**JDK proxies require an interface. CGLIB** subclasses the class instead, which is why Spring uses
CGLIB for classes without interfaces — and why `final` classes and `final` methods can't be proxied.

## Where reflection appears in your stack

| Framework | Uses it for |
|---|---|
| **Spring** | Component scanning, DI, AOP proxies, `@Transactional`, auto-configuration |
| **Hibernate/JPA** | Instantiating entities, populating fields without setters |
| **Jackson** | Mapping JSON to fields, `@JsonProperty` |
| **JUnit** | Finding and invoking `@Test` methods |
| **Lombok** | Actually *not* reflection — it's a compile-time annotation processor that edits the AST |

**Lombok is the interesting exception** — `@Data` and `@Builder` generate real methods at compile
time, so there's no runtime cost at all. Knowing that distinction is a good detail.

## When not to use it

- **Never as a substitute for good design.** Reflecting into another class's privates couples you to
  its implementation.
- **Not in a hot path** without caching the lookups.
- **Not where an interface would do.** A strategy interface is checked at compile time; reflection
  isn't.

**Use it when the type genuinely isn't known until runtime** — frameworks, serialisation, plugin
systems, test runners.

---

## Type this yourself

```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

public class ReflectionDemo {

    // ---- custom annotations ----
    @Retention(RetentionPolicy.RUNTIME) @Target(ElementType.METHOD)
    @interface Test { }

    @Retention(RetentionPolicy.RUNTIME) @Target(ElementType.METHOD)
    @interface Timed { String value() default ""; }

    @Retention(RetentionPolicy.RUNTIME) @Target(ElementType.FIELD)
    @interface Inject { }

    // ---- a class to reflect over ----
    static class User {
        private String name;
        private int age;
        @Inject private String service;

        public User() { }
        public User(String name, int age) { this.name = name; this.age = age; }
        private void secret() { System.out.println("      secret() called reflectively"); }
        public String getName() { return name; }
        @Override public String toString() { return "User[" + name + ", " + age + ", " + service + "]"; }
    }

    // ---- a 15-line JUnit ----
    static class MyTests {
        @Test void passes()  { assert 1 + 1 == 2; }
        @Test void fails()   { throw new IllegalStateException("deliberate"); }
        void notATest()      { throw new RuntimeException("should never run"); }
    }

    interface Service { String work(String input); }
    static class RealService implements Service {
        public String work(String input) {
            try { Thread.sleep(20); } catch (InterruptedException ignored) {}
            return input.toUpperCase();
        }
    }

    public static void main(String[] args) throws Exception {

        // ---- 1. inspect ----
        System.out.println("--- inspecting User ---");
        Class<?> c = User.class;
        System.out.println("   name: " + c.getName());
        for (Field f : c.getDeclaredFields())
            System.out.printf("   field %-8s %-16s annotations=%s%n",
                    f.getName(), f.getType().getSimpleName(),
                    Arrays.toString(f.getDeclaredAnnotations()));

        // ---- 2. instantiate + populate WITHOUT setters (what Hibernate does) ----
        System.out.println("\n--- reflective instantiation ---");
        User u = (User) c.getDeclaredConstructor().newInstance();
        Field nameField = c.getDeclaredField("name");
        nameField.setAccessible(true);                    // bypass private
        nameField.set(u, "Ramesh");
        Field ageField = c.getDeclaredField("age");
        ageField.setAccessible(true);
        ageField.setInt(u, 25);
        System.out.println("   " + u + "   ← no setters were called");

        // ---- 3. invoke a private method ----
        System.out.println("\n--- invoking a private method ---");
        Method secret = c.getDeclaredMethod("secret");
        secret.setAccessible(true);
        secret.invoke(u);

        // ---- 4. field injection, the way Spring does @Autowired ----
        System.out.println("\n--- @Inject field injection ---");
        for (Field f : c.getDeclaredFields()) {
            if (f.isAnnotationPresent(Inject.class)) {
                f.setAccessible(true);
                f.set(u, "INJECTED-SERVICE");
                System.out.println("   injected into " + f.getName());
            }
        }
        System.out.println("   " + u);

        // ---- 5. a test runner ----
        System.out.println("\n--- 15-line JUnit ---");
        runTests(MyTests.class);

        // ---- 6. a dynamic proxy — this is Spring AOP ----
        System.out.println("\n--- dynamic proxy ---");
        Service real = new RealService();
        Service proxied = (Service) Proxy.newProxyInstance(
                Service.class.getClassLoader(),
                new Class<?>[]{ Service.class },
                (p, method, methodArgs) -> {
                    long start = System.nanoTime();
                    try { return method.invoke(real, methodArgs); }
                    finally {
                        System.out.printf("   [proxy] %s took %,d µs%n",
                                method.getName(), (System.nanoTime() - start) / 1000);
                    }
                });
        System.out.println("   result = " + proxied.work("hello"));
        System.out.println("   ↑ the timing was added WITHOUT touching RealService");

        // ---- 7. the cost ----
        System.out.println("\n--- performance ---");
        int n = 20_000_000;
        User target = new User("x", 1);

        long start = System.nanoTime();
        String s = null;
        for (int i = 0; i < n; i++) s = target.getName();
        long direct = System.nanoTime() - start;

        Method cached = c.getMethod("getName");
        start = System.nanoTime();
        for (int i = 0; i < n; i++) s = (String) cached.invoke(target);
        long cachedTime = System.nanoTime() - start;

        start = System.nanoTime();
        for (int i = 0; i < 200_000; i++) s = (String) c.getMethod("getName").invoke(target);
        long uncached = (System.nanoTime() - start) * (n / 200_000);

        System.out.printf("   direct call:        %,6d ms%n", direct / 1_000_000);
        System.out.printf("   cached Method:      %,6d ms  (%.1f×)%n",
                cachedTime / 1_000_000, (double) cachedTime / direct);
        System.out.printf("   getMethod() each:   %,6d ms  (%.0f×, extrapolated)%n",
                uncached / 1_000_000, (double) uncached / direct);
        System.out.println("   ↑ LOOKUP is the cost, not invocation. Cache it. (" + s + ")");

        // ---- 8. generic signatures survive erasure ----
        System.out.println("\n--- generics survive in metadata (Day 055) ---");
        class Holder { List<String> names; Map<String, List<Integer>> data; }
        for (Field f : Holder.class.getDeclaredFields())
            System.out.println("   " + f.getName() + " : " + f.getGenericType());
    }

    static void runTests(Class<?> testClass) throws Exception {
        Object instance = testClass.getDeclaredConstructor().newInstance();
        int pass = 0, fail = 0;
        for (Method m : testClass.getDeclaredMethods()) {
            if (!m.isAnnotationPresent(Test.class)) continue;
            m.setAccessible(true);
            try { m.invoke(instance); System.out.println("   PASS " + m.getName()); pass++; }
            catch (InvocationTargetException e) {
                System.out.println("   FAIL " + m.getName() + " — " + e.getCause().getMessage());
                fail++;
            }
        }
        System.out.printf("   %d passed, %d failed%n", pass, fail);
    }
}
```

**Five things to take from the output:**

1. **Fields were populated with no setters** — that's Hibernate.
2. **A private method was invoked** — `setAccessible` defeating `private`.
3. **The `@Inject` field was filled by scanning annotations** — that's `@Autowired`, in six lines.
4. **The proxy added timing without touching `RealService`** — that's AOP, and the mechanism behind
   `@Transactional`.
5. **The timing table shows lookup dominates.** Cache the `Method`.

---

## Common mistakes

| Mistake | Correction |
|---|---|
| Forgetting `@Retention(RUNTIME)` | The annotation is invisible to reflection and silently does nothing. |
| Calling `getMethod` in a loop | Lookup is the expensive part. Cache it. |
| Confusing `getFields` and `getDeclaredFields` | Public-and-inherited versus all-access-but-not-inherited. |
| Expecting `@Inherited` to work on methods | It applies to class annotations only. |
| Reflection where an interface would do | Loses compile-time checking for no benefit. |
| Assuming `setAccessible` always works | Restricted across module boundaries since Java 9. |
| Not unwrapping `InvocationTargetException` | The real exception is in `getCause()`. |

---

## Interview questions

**Q: What is reflection and what is it for?**
> The ability to inspect and manipulate types, methods and fields at runtime by name, rather than
> resolving them at compile time. It's what every framework is built on — Spring's dependency
> injection and component scanning, Hibernate instantiating entities and populating private fields,
> Jackson mapping JSON, JUnit discovering test methods.

**Q: How do custom annotations work?**
> An annotation is inert metadata — it does nothing by itself. Something has to read it and act. You
> declare it with `@interface` and set `@Retention(RUNTIME)` so it survives into the class file and is
> readable reflectively, plus `@Target` to constrain where it applies. Then a processor scans for it
> with `isAnnotationPresent` and behaves accordingly. Forgetting `RUNTIME` is the classic mistake —
> the annotation compiles fine and is silently invisible.

**Q: How does Spring's `@Transactional` actually work?**
> Spring creates a proxy around the bean — a JDK dynamic proxy if it implements interfaces, otherwise
> a CGLIB subclass. Calls go through an `InvocationHandler` that begins a transaction, delegates to
> the real object, then commits or rolls back. That's also why self-invocation fails: an internal
> `this.method()` call goes straight to the target and bypasses the proxy, so the handler never runs
> and no transaction is started.

**Q: Is reflection slow?**
> Slower than direct calls, but the dominant cost is *lookup*, not invocation. A cached `Method`
> object invoked repeatedly is only a few times slower than a direct call; calling `getMethod` inside
> the loop is an order of magnitude worse. Frameworks resolve everything at startup and cache it,
> which is why Spring Boot starts slowly and then runs at normal speed.

**Q: What are the downsides beyond performance?**
> No compile-time checking — a renamed method becomes a runtime failure. It breaks encapsulation via
> `setAccessible`, coupling you to another class's internals. The JIT can't inline through most
> reflective calls. And since Java 9 it's restricted across module boundaries unless the package is
> explicitly opened.

**Q: What's the difference between reflection and an annotation processor like Lombok?**
> Reflection happens at runtime and costs at runtime. An annotation processor runs at compile time and
> generates real source or bytecode — Lombok's `@Data` produces actual getters and setters in the class
> file, so there's no runtime reflection and no cost at all.

---

## Mini task

1. Run `ReflectionDemo` and record the three timing figures.
2. **Write your own mini DI container**: scan a class for `@Inject` fields and populate them from a
   `Map<Class<?>, Object>` registry.
3. Extend the test runner with `@Before` and `@After` support.
4. Write a `@Retry(times = 3)` annotation and a dynamic proxy that honours it.
5. Prove the `@Transactional` self-invocation problem: build a proxy that logs, then call one proxied
   method from inside another and watch the log not appear.

---

# 🚪 Exit questions

1. What is reflection, and what does `Class.forName` do that a class literal doesn't?
2. `getFields` vs `getDeclaredFields`?
3. What does `setAccessible(true)` defeat, and what restricts it since Java 9?
4. Why do JPA entities need a no-arg constructor?
5. Name the three retention policies and which one reflection requires.
6. Why does an annotation do nothing on its own?
7. Describe how a dynamic proxy works, and what it needs.
8. **Explain why `@Transactional` fails on self-invocation.**
9. Where is reflection's cost, and how do frameworks avoid it?
10. How does Lombok differ from a reflective framework?

## 🎙️ Articulation drill

Two minutes: **"How does Spring actually inject a dependency into a private field with no setter?"**

Component scan → reflection → `getDeclaredFields` → check for the annotation → `setAccessible(true)`
→ `field.set`. Six steps, no magic. **That's the answer this day exists to give you.**

---

**Previous:** [Day 056](Day-056.md) · **Tomorrow:** [Day 057](Day-057.md) — lambdas and functional
interfaces
