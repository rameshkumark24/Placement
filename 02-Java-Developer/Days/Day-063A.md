# Day 063A ➕

**L-063A** · Java serialization — `Serializable`, `serialVersionUID`, `transient` · **why it is an
RCE hazard**, and why you use JSON instead

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** The roadmap covers I/O and later covers REST payloads, but never
> Java's native serialization. That leaves two holes: you will meet `implements Serializable` in
> every legacy codebase and every JPA entity and not know what it means, and you will not recognise
> **the single most severe vulnerability class in Java's history** when you see it. Oracle's own
> architect called it "the gift that keeps on giving" — and there is an active JEP to remove it.
> You need to be able to spot it and to say why you would never enable it.

---

# Part 1 — L-063A · What serialization is

**Serialization turns an object graph into a byte stream; deserialization turns the bytes back into
objects.** Not just one object — the whole reachable graph, following every non-transient reference,
handling cycles and shared references so that identity is preserved.

```java
import java.io.*;

class Point implements Serializable {
    private static final long serialVersionUID = 1L;
    int x, y;
    Point(int x, int y) { this.x = x; this.y = y; }
    public String toString() { return "(" + x + "," + y + ")"; }
}

public class SerDemo {
    public static void main(String[] args) throws Exception {
        Point p = new Point(3, 4);

        var bytes = new ByteArrayOutputStream();
        try (var out = new ObjectOutputStream(bytes)) {
            out.writeObject(p);
        }
        System.out.println("serialized to " + bytes.size() + " bytes");
        System.out.println(java.util.HexFormat.of().formatHex(
                java.util.Arrays.copyOf(bytes.toByteArray(), 8)));

        try (var in = new ObjectInputStream(new ByteArrayInputStream(bytes.toByteArray()))) {
            Point q = (Point) in.readObject();
            System.out.println(q + "  same object? " + (p == q));
        }
    }
}
```

Run it and look at the first bytes: `ACED0005`. That is the serialization stream magic number and
version — if you ever see `rO0AB` at the start of a base64 string in a cookie, a hidden form field,
or a request body, **that is base64-encoded `ACED0005`**, and you are looking at a Java serialized
object crossing a trust boundary. Recognising that string on sight is worth the whole day.

## The mechanics you must know

**`Serializable` is a marker interface** — no methods. It is a permission flag: "the JVM may
serialize me." Fields are written by reflection, bypassing your constructor entirely.

**`serialVersionUID` is the compatibility contract.** If you do not declare one, the JVM computes it
from a hash of the class's name, fields, methods and modifiers. Add a method — a *method* — and the
hash changes, and every previously written byte stream now fails with `InvalidClassException`.
Always declare it explicitly on any class you mark `Serializable`.

**`transient` excludes a field.** It is restored as the type default (`null`, `0`, `false`) — not
recomputed. Any invariant that depended on it is now broken unless you fix it in `readObject`.
Use it for caches, derived values, non-serializable references, and secrets.

**`static` fields are never serialized.** They belong to the class, not the instance.

**Deserialization does not call your constructor.** This is the fact everything else follows from.
`ObjectInputStream` allocates the object directly and populates fields reflectively. Every
validation you wrote in the constructor (Day 062's fail-fast, Day 034A's defensive copies) is
**skipped**. So:

```java
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    in.defaultReadObject();
    if (x < 0 || y < 0) throw new InvalidObjectException("negative coordinate");
    this.tags = List.copyOf(this.tags);      // re-establish immutability
}
```

Those magic private methods — `writeObject`, `readObject`, `readResolve`, `writeReplace` — are
called by reflection. They are, effectively, a **second constructor with no compiler checks on it**,
which is why Joshua Bloch calls serialization an "extralinguistic mechanism".

`readResolve` also matters for a pattern you already met: it is what stops deserialization from
breaking a singleton by producing a second instance (Day 043's argument for the enum singleton —
enums are serialization-safe by specification, which is one of the reasons enum is the correct
singleton).

---

# Part 2 — Why it is an RCE hazard

## The attack, precisely

The vulnerability is not "attackers can send fake data". It is much worse:

> **`readObject()` executes code from classes on your classpath, chosen by the attacker, before you
> ever see the result.**

Look at the order of operations. `ObjectInputStream.readObject()` must reconstruct the graph, so it
reads a class name from the stream, loads that class, instantiates it, populates its fields, and —
crucially — **calls that class's `readObject` / `readResolve` / `hashCode` / `equals` / `compareTo`
as needed**. Only then does it return, and only then does your code perform its cast:

```java
MyDto dto = (MyDto) in.readObject();   // ← the cast happens AFTER arbitrary code has run
```

The `ClassCastException` you might be relying on arrives far too late.

## Gadget chains

The attacker does not need a vulnerable class of yours. They need a **gadget chain**: a sequence of
classes already on your classpath whose deserialization side-effects can be strung together into
something dangerous. The classic chain used Apache Commons Collections' `InvokerTransformer`, which
invokes a method by name via reflection, wrapped in a `ChainedTransformer`, triggered by a
`HashMap`'s `hashCode()` on a `TiedMapEntry`, and terminating in
`Runtime.getRuntime().exec("...")`.

```
attacker's bytes
   └─► HashMap.readObject()
         └─► key.hashCode()                 (called to rebuild the table)
               └─► TiedMapEntry.hashCode()
                     └─► LazyMap.get()
                           └─► ChainedTransformer.transform()
                                 └─► InvokerTransformer → Runtime.exec("curl … | sh")
```

Every link is ordinary, well-intentioned library code. None of it is a bug in isolation. **The
composition is the exploit**, which is why "just patch the vulnerable library" never fully worked —
new chains keep being found. The `ysoserial` tool ships dozens of them.

This produced some of the largest breaches on record. The 2017 Equifax breach was Apache Struts;
several of the WebLogic, JBoss and Jenkins critical CVEs were deserialization. It is CWE-502 and it
appears in the OWASP Top 10 under software and data integrity failures — the same list the Web
domain's Phase 06 uses.

## What does *not* fix it

| "Fix" | Why it fails |
|---|---|
| "I cast to my type afterwards" | Code already ran before the cast |
| "The payload is encrypted/signed" | Fixes tampering only — if the key leaks, RCE returns |
| "I removed Commons Collections" | Dozens of other chains exist, including JDK-internal ones |
| "It's internal traffic only" | One SSRF or one compromised service and it is not |
| "I validate the fields after" | After is too late |

## What actually works

**1. Do not deserialize untrusted data with `ObjectInputStream`. At all.** This is the real answer
and the one to lead with in an interview.

**2. Use a data format, not an object format.** JSON, Protobuf, Avro, CBOR describe *data*. They
cannot name a class to instantiate, so there is no code-execution primitive at all. This is why
every modern API uses JSON — not because it is prettier, but because the format is inert.

*Caveat worth knowing:* Jackson can be made vulnerable too, if you enable
`enableDefaultTyping()` / `@JsonTypeInfo` with an open base type, because that reintroduces
"the payload names the class". Leave polymorphic typing off, or use an explicit allow-list of
subtypes.

**3. If you truly cannot avoid it, filter.** Java 9+ has a built-in serialization filter (JEP 290):

```java
ObjectInputFilter filter = ObjectInputFilter.Config.createFilter(
        "com.acme.dto.*;java.base/java.lang.*;java.util.List;!*");   // allow-list, deny the rest
ois.setObjectInputFilter(filter);
```

Or JVM-wide with `-Djdk.serialFilter=...`. **Allow-list, never deny-list** — a deny-list is a race
against every gadget not yet discovered. Java 17 added `ObjectInputFilter.allowFilter` for a
context-specific version.

**4. Prefer designs that never mark classes `Serializable`.** Every `Serializable` class is a
permanent public API surface — its private fields become part of the wire format and you can never
change them freely again.

---

## Where you will still meet it

- **JPA/Hibernate entities** implement `Serializable` by convention (needed for detached entities
  and second-level caches). Fine — the data never crosses a trust boundary.
- **HTTP session replication** in clustered servlet containers serializes session attributes. This
  is a real trust-boundary question: if session state travels over the network, sign and restrict it.
- **RMI, JMX, JNDI** are built on it. Exposed RMI/JMX ports are a standing risk; never expose them.
- **Old caches** (memcached/EhCache with Java serialization) — same trust-boundary question.

Grep any codebase you inherit for `ObjectInputStream`. Every occurrence deserves an explanation.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| `ObjectInputStream` on external input | Remote code execution |
| No `serialVersionUID` | `InvalidClassException` after a harmless refactor |
| Expecting the constructor to run | Every invariant bypassed |
| `transient` without a `readObject` fix-up | Field silently null/zero |
| Jackson `enableDefaultTyping` | Re-creates the same vulnerability in JSON |
| Deny-list filters | Broken by the next gadget chain |
| Serializing secrets | Keys and passwords sitting in a cache or on disk |

---

## Interview questions

**Q: What is `serialVersionUID` for?**
Version compatibility. Absent an explicit value, it is a hash of the class structure, so any
structural change breaks deserialization of existing streams.

**Q: Does deserialization call the constructor?**
No. The object is allocated and fields set reflectively, so constructor validation is bypassed —
which is why `readObject` must re-validate and re-copy.

**Q: Why is Java deserialization dangerous?**
Because the byte stream names the classes to instantiate, and reconstructing the graph invokes
methods on them — `readObject`, `hashCode`, `equals` — before your code sees anything. Chains of
ordinary library classes can be composed to reach `Runtime.exec`. The fix is not to deserialize
untrusted data at all; use JSON or Protobuf, and if forced, apply an allow-list filter.

**Q: How would you serialize a singleton safely?**
Use an enum. Otherwise implement `readResolve` to return the canonical instance.

**Q: `transient` versus `static` in serialization?**
Both are excluded, for different reasons: `transient` says "do not persist this instance field";
`static` belongs to the class and was never part of instance state.

---

## Mini task

1. Run `SerDemo`. Confirm the `ACED0005` header. Base64-encode the bytes and confirm the `rO0AB`
   prefix — memorise it.
2. Serialize a `Point`, then add a method to the class, recompile, and try to deserialize the old
   bytes. Observe `InvalidClassException`. Add an explicit `serialVersionUID` and repeat.
3. Write a class whose constructor rejects negative values. Serialize a valid instance, flip the
   sign byte in the stream by hand, deserialize it, and print the object. Confirm the invalid object
   exists. Then add `readObject` validation and confirm it is now rejected.
4. Apply an `ObjectInputFilter` allow-listing only your class and confirm anything else is refused.
5. Convert the same object to JSON with Jackson and note that no class name appears in the output.

---

# 🚪 Exit questions

1. What does `Serializable` actually do, given it has no methods?
2. What breaks if you omit `serialVersionUID`?
3. Why is deserialization dangerous *before* the cast on your side?
4. Explain a gadget chain in two sentences.
5. Why does removing one vulnerable library not solve it?
6. Why is JSON structurally safer than Java serialization?
7. What Jackson setting reintroduces the vulnerability?
8. Allow-list or deny-list for a serialization filter, and why?
9. Why are enums the serialization-safe singleton?
10. What does `rO0AB` at the start of a request parameter tell you?

## 🎙️ Articulation drill

Two minutes: **"Why is Java serialization considered dangerous, and what do you use instead?"**

Say the mechanism — the stream names classes, reconstruction runs their code, the cast is too late —
then the mitigation ladder: do not do it, use an inert data format, and if forced, allow-list filter.
Naming CWE-502 and the Struts/Equifax example makes it concrete without sounding rehearsed.

---

**Previous:** [Day 063](Day-063.md) · **Next:** [Day 063B](Day-063B.md) — ➕ regex and ReDoS

> Same shape tomorrow: a feature everyone uses casually that is also a denial-of-service vector if
> you do not understand its engine.
