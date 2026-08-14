# Day 076A ➕

**L-076A** · Classpath & class loading in practice — `ClassNotFoundException` vs
`NoClassDefFoundError`, jar hell, `mvn dependency:tree`

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** Day 023 covered the class loader as JVM architecture — the delegation
> model, the three loaders. What the roadmap never does is connect that to the errors you will
> actually hit: `ClassNotFoundException`, `NoClassDefFoundError`, `NoSuchMethodError`,
> `LinkageError`, and the version conflicts from Day 073. These are among the most confusing
> failures in Java precisely because **the code compiles perfectly and fails at runtime**. This day
> makes Day 023's theory operational.

---

# Part 1 — The classpath

## What it actually is

The classpath is **an ordered list of places to look for `.class` files**. That is all. Directories
and jars, searched left to right, first match wins.

```bash
java -cp "lib/a.jar:lib/b.jar:classes" com.acme.Main      # : on Unix
java -cp "lib\a.jar;lib\b.jar;classes" com.acme.Main      # ; on Windows
java -cp "lib/*" com.acme.Main                             # every jar in lib/ (NOT recursive)
java -jar app.jar                                          # ← -cp is IGNORED. See below.
```

Two facts that cause real confusion:

**`-jar` ignores `-cp` entirely.** With `-jar`, the classpath comes solely from the jar's
`META-INF/MANIFEST.MF` `Class-Path:` entry. Adding `-cp` alongside `-jar` silently does nothing —
people spend an hour on this.

**`lib/*` is expanded by the JVM, not the shell**, so quote it, and it is not recursive —
`lib/sub/x.jar` is not included.

**Order matters.** If two jars contain `com.acme.Util`, the one earlier on the classpath wins and
the other is invisible. There is no warning. That is the mechanism behind most "jar hell".

## The delegation model, and where it bites

From Day 023, with the practical consequences added:

```
Bootstrap  (java.base — the JDK itself; written in native code, parent of everything)
    ▲
Platform   (JDK modules: java.sql, java.xml…)
    ▲
Application/System  (your -cp: your code and your dependencies)
    ▲
Custom     (web apps, plugins, OSGi, Spring Boot's LaunchedURLClassLoader)
```

**Parent-first delegation:** a loader asks its parent before trying itself. So `java.lang.String`
always comes from Bootstrap — you cannot shadow a JDK class by putting your own on the classpath.
That is a security property, not a convenience.

Two important inversions:

- **Servlet containers use child-first (parent-last) for webapps**, so a webapp can bundle its own
  version of a library that the container also has. Tomcat and the webapp each get their own.
- **A class's identity is `(fully-qualified name, class loader)`.** The same `com.acme.User` loaded
  by two loaders is **two different classes**. That is why you can get the baffling
  `ClassCastException: com.acme.User cannot be cast to com.acme.User` — same name, different loader,
  and the message looks like a JVM bug. It is not. It is almost always a redeploy or a plugin/OSGi
  boundary.

## The three-phase lifecycle — and why it explains the errors

Day 023 named these. Here is why they matter:

| Phase | What happens | Failure |
|---|---|---|
| **Loading** | Find the bytes, build the `Class` object | `ClassNotFoundException` |
| **Linking** | Verify → prepare (statics get defaults) → resolve | `NoClassDefFoundError`, `NoSuchMethodError`, `VerifyError` |
| **Initialization** | Run static initialisers and `static {}` | `ExceptionInInitializerError` |

**Initialization is lazy** — it happens on first *active use*, not at load. That is exactly why Day
065's holder-idiom singleton works, and why a `static {}` block's exception shows up somewhere
apparently unrelated.

---

# Part 2 — The five errors, precisely

## `ClassNotFoundException` — the class was never found

**Checked exception. Thrown by explicit, dynamic loading.**

```java
Class.forName("com.mysql.cj.jdbc.Driver");        // ← throws ClassNotFoundException
classLoader.loadClass("com.acme.Plugin");
```

Means: *someone asked for a class by name, at runtime, and it is not on the classpath.*

Causes: the jar is missing from the runtime classpath; the name is misspelled; it was `provided`
scope (Day 073) so it compiled but was not packaged; a fat jar was built without it.

## `NoClassDefFoundError` — it was there at compile time, not now

**An `Error`, not an exception. Thrown by the JVM during linking.**

```java
Helper h = new Helper();     // compiled fine; Helper.class absent at runtime
```

Means: *the compiler saw this class, so it existed then; the JVM cannot find it now.* It is a
**deployment/packaging mismatch**, which is the crucial diagnostic difference:

> `ClassNotFoundException` — you asked for it by name and it is not there.
> `NoClassDefFoundError` — the compiler saw it, the JVM cannot.

**The second cause of `NoClassDefFoundError` catches everyone**, and it is worth its own space:

```java
class Config {
    static final String VALUE = System.getenv("REQUIRED").trim();   // NPE if unset
}

Config.get();   // 1st call: ExceptionInInitializerError (NPE inside <clinit>)
Config.get();   // 2nd call: NoClassDefFoundError: Could not initialize class Config
```

Once a class's static initialiser throws, the class is marked **erroneous, permanently**. Every
subsequent use gets `NoClassDefFoundError: Could not initialize class …` — with no hint of the
original cause. So:

> **If you see "Could not initialize class X", the real error happened earlier. Search the logs
> upward for the first `ExceptionInInitializerError`.**

That single sentence has saved people entire days.

## `NoSuchMethodError` — version mismatch

```
java.lang.NoSuchMethodError: 'void com.google.common.base.Preconditions.checkArgument(boolean, String)'
```

**The definitive signature of two different versions of the same library.** You compiled against
version 31 (which has the method) and are running against version 28 (which does not) — or vice
versa.

The cause is Day 073's resolution rule: Maven picked the **nearest**, not the newest.

```bash
mvn dependency:tree -Dverbose -Dincludes=com.google.guava
```

`-Dverbose` prints the versions that were *omitted for conflict* — that is the smoking gun. Then fix
with an exclusion, `dependencyManagement`, or a BOM.

`NoSuchFieldError` and `AbstractMethodError` are the same disease: `AbstractMethodError` means an
implementation was compiled against an older interface that lacked a method later added.

## `LinkageError` / `ClassCastException: X cannot be cast to X`

Two loaders, one class name. The redeploy or plugin-boundary case above.

## `UnsupportedClassVersionError` — wrong Java version

```
UnsupportedClassVersionError: com/acme/Main has been compiled by a more recent version
of the Java Runtime (class file version 65.0), this version ... recognizes up to 61.0
```

Class file versions: **45 = Java 1.1**, and then **44 + N**: 52 = Java 8, 55 = 11, 61 = 17, 65 = 21.
Memorise 52/61/65 and you can read this error instantly.

The fix is `maven.compiler.release` (not `source`/`target` — `release` also checks that you only use
APIs present in that version, which `target` does not).

---

# Part 3 — Fat jars, and why Spring Boot needed its own loader

A jar cannot contain another jar and have the JVM find classes inside it — the standard class loader
does not look inside nested jars. So there are two strategies:

**Shading (Maven Shade / Gradle Shadow):** unpack every dependency and re-jar all the `.class` files
together, flat.

- ⚠️ **File collisions.** Two jars both containing `META-INF/services/javax.script.ScriptEngineFactory`
  — one silently overwrites the other, and service loading breaks. Shade's
  `ServicesResourceTransformer` merges them; forgetting it is a classic bug.
- ⚠️ **Signature invalidation.** Signed jars' `META-INF/*.SF` no longer match, producing
  `SecurityException: Invalid signature file digest`. Filter them out.
- ✅ **Relocation** is shading's superpower: rewrite `com.google.common` → `com.acme.shaded.guava`
  inside your jar, so your Guava cannot conflict with your consumer's. This is how libraries avoid
  imposing dependency versions on users.

**Spring Boot's nested jars:** keep dependency jars intact under `BOOT-INF/lib/` and ship a custom
`LaunchedURLClassLoader` that can read them. No collisions, no relocation needed, and
`java -jar app.jar` works. The manifest's `Main-Class` is Boot's `JarLauncher`, which then loads
*your* `Start-Class` — which is why the manifest looks unfamiliar the first time you read it.

## Practical inspection

```bash
jar tf app.jar | head -50                          # what is inside
unzip -p app.jar META-INF/MANIFEST.MF              # Main-Class, Class-Path

# which jar does a class come from? — the most useful trick here
unzip -l "lib/*.jar" | grep -i "Preconditions.class"
jar tf lib/guava-31.jar | grep Preconditions

# what the JVM thinks it loaded, at runtime
java -verbose:class -jar app.jar | grep Preconditions
#   [class,load] com.google.common.base.Preconditions source: file:/…/guava-28.0-jre.jar
```

**`-verbose:class` is the definitive answer** to "which jar did this class actually come from". When
`dependency:tree` and reality disagree — a shaded jar, a container-provided library, an
`AppClassLoader` surprise — this is what settles it. In code:

```java
System.out.println(Preconditions.class.getProtectionDomain().getCodeSource().getLocation());
System.out.println(Preconditions.class.getClassLoader());
```

Two lines you can paste into a running application to end an argument.

## Code to type — cause each error deliberately

```java
public class LoaderLab {
    public static void main(String[] args) {
        // 1. ClassNotFoundException — dynamic lookup of something absent
        try { Class.forName("com.acme.DoesNotExist"); }
        catch (ClassNotFoundException e) { System.out.println("1: " + e); }

        // 2. ExceptionInInitializerError, then NoClassDefFoundError
        try { Broken.touch(); } catch (Throwable t) { System.out.println("2a: " + t); }
        try { Broken.touch(); } catch (Throwable t) { System.out.println("2b: " + t); }

        // 3. Where did this class come from?
        System.out.println("3: " + String.class.getClassLoader());              // null = Bootstrap
        System.out.println("3: " + LoaderLab.class.getClassLoader());           // AppClassLoader
        System.out.println("3: " + LoaderLab.class.getProtectionDomain()
                                            .getCodeSource().getLocation());

        // 4. Lazy initialization
        System.out.println("4: about to touch Lazy");
        System.out.println("4: " + Lazy.VALUE);
    }

    static class Broken {
        static final int X = 1 / Integer.parseInt("0");    // throws in <clinit>
        static void touch() { System.out.println(X); }
    }

    static class Lazy {
        static { System.out.println("4: Lazy <clinit> ran NOW, not at load"); }
        static final String VALUE = "loaded";
    }
}
```

Run it. **Line 2a is `ExceptionInInitializerError` and 2b is
`NoClassDefFoundError: Could not initialize class …`** — the same class, the same call, two
different errors, and the second one has lost the cause entirely. That contrast is the most useful
thing in this day.

Then build the packaging failures for real:

```bash
javac -d classes LoaderLab.java Helper.java
java -cp classes LoaderLab                # works
rm classes/Helper.class
java -cp classes LoaderLab                # NoClassDefFoundError
```

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| `-cp` together with `-jar` | Silently ignored; only the manifest counts |
| Assuming `lib/*` is recursive | Subdirectory jars missing |
| Two versions of a library on the classpath | `NoSuchMethodError` at runtime, nothing at compile |
| Chasing "Could not initialize class" as the cause | The real error was the earlier `ExceptionInInitializerError` |
| `provided` scope for a runtime need | `ClassNotFoundException` in production only |
| Shading without merging `META-INF/services` | Service loading silently broken |
| Shading signed jars | `Invalid signature file digest` |
| `source`/`target` instead of `release` | Compiles against newer APIs than the target JVM has |
| Treating `ClassCastException: X to X` as a JVM bug | It is two class loaders |

---

## Interview questions

**Q: `ClassNotFoundException` vs `NoClassDefFoundError`?**
The first is a checked exception from an explicit dynamic lookup — `Class.forName` — for something
not on the classpath. The second is an `Error` from the JVM: the class was present at compile time
but is missing (or failed to initialise) at runtime. Roughly: "you asked and it isn't there" versus
"the compiler saw it and the JVM can't".

**Q: You get `NoClassDefFoundError: Could not initialize class Foo`. What happened?**
`Foo`'s static initialiser threw earlier — the first occurrence was an `ExceptionInInitializerError`.
The class is permanently marked erroneous, so every later use gets this message. Search the log
upward for the original.

**Q: What causes `NoSuchMethodError`?**
Two versions of a library — compiled against one, running against another. Diagnose with
`mvn dependency:tree -Dverbose`, fix with an exclusion, `dependencyManagement`, or a BOM.

**Q: Why can `X cannot be cast to X` happen?**
Class identity is name plus class loader. Two loaders produce two distinct classes with the same
name — typically a redeploy or a plugin boundary.

**Q: Explain the delegation model and one place it is deliberately inverted.**
Each loader asks its parent first, so JDK classes always come from Bootstrap and cannot be shadowed.
Servlet containers invert it for webapps (child-first) so an application can bundle its own version
of a library the container also provides.

**Q: How do you find which jar a class was loaded from?**
`-verbose:class` at startup, or at runtime
`Foo.class.getProtectionDomain().getCodeSource().getLocation()`.

**Q: How does Spring Boot make an executable jar?**
Nested jars under `BOOT-INF/lib/` plus a custom `LaunchedURLClassLoader` that reads inside them,
launched via `JarLauncher`. This avoids the file collisions and signature problems of shading.

---

## Mini task

1. Run `LoaderLab` and confirm 2a and 2b differ. Explain why in writing.
2. Compile two classes, delete one `.class`, and reproduce `NoClassDefFoundError`.
3. Put two versions of a small library on the classpath in each order and show that order decides
   which wins.
4. Deliberately create a `NoSuchMethodError` with two Guava versions, then find it with
   `dependency:tree -Dverbose`, then fix it with `dependencyManagement`.
5. Compile with `--release 21` and run on a Java 17 JVM. Read the class file version numbers in the
   error and map them to Java versions.
6. Build a shaded jar containing two libraries that each ship `META-INF/services` entries. Break
   service loading, then fix it with `ServicesResourceTransformer`.
7. Run any Spring Boot jar with `-verbose:class | head -50` and identify `JarLauncher` and
   `LaunchedURLClassLoader`.
8. Print the class loader and code source for one of your own classes, one JDK class, and one
   dependency class.

---

# 🚪 Exit questions

1. What is the classpath, mechanically, and what decides a tie?
2. Why does `-cp` do nothing alongside `-jar`?
3. State the delegation model and the security property it provides.
4. Where is delegation inverted, and why?
5. What are the two components of a class's identity?
6. Name the three lifecycle phases and the error each produces.
7. Distinguish `ClassNotFoundException` and `NoClassDefFoundError` in one sentence each.
8. What does "Could not initialize class X" tell you to go and look for?
9. What single condition produces `NoSuchMethodError`, and what is the first command you run?
10. Map class file versions 52, 61 and 65 to Java releases.
11. Give two hazards of shading and the fix for each.
12. How does Spring Boot avoid both of them?

## 🎙️ Articulation drill

Two minutes: **"Your application compiles and starts, then throws `NoSuchMethodError` on a library
call. Diagnose it."**

Two versions on the classpath → why compile-time cannot catch it → `dependency:tree -Dverbose` for
the omitted-for-conflict lines → confirm with `-verbose:class` which jar actually supplied it → fix
with exclusion or `dependencyManagement`/BOM. Then mention that the same investigation is how you
answer "are we exposed to this CVE" — which links it straight back to Day 073A's Log4Shell point.

---

**Previous:** [Day 076](Day-076.md) · **Next:** [Day 077](Day-077.md) — Java interview traps drill

> Tomorrow closes Stage 1: forty gotcha questions across everything from Day 023 to today, then the
> exit gate.
