# Day 163b ➕

**L-163b** · Injection safety in JPA — where the ORM protects you, and where it does not

**Time:** 3 hrs · **Mode:** NEW · **Added gap day**

> Day 119b established the general principle: **injection happens when data crosses into a code
> context.** Parameterisation works because the query structure is fixed before any value arrives.
>
> There is a widespread belief that using an ORM makes injection impossible. **It does not.** JPA has
> at least six ways to build a query, and two of them are string concatenation with extra steps.
> Today is exactly where the boundary sits, plus the injection classes an ORM cannot help with at
> all — because they are not SQL.

---

# Part 1 — What is safe, and precisely why

```java
// ✅ SAFE — derived query. Structure generated from the method name; values bound.
List<Order> findByCustomerNameAndStatus(String name, OrderStatus status);

// ✅ SAFE — named parameters. JPQL parsed to a tree; :name is a bind slot.
@Query("select o from Order o where o.customerName = :name")
List<Order> byName(@Param("name") String name);

// ✅ SAFE — native query with bound parameters.
@Query(value = "select * from orders where customer_name = :name", nativeQuery = true)
List<Order> byNameNative(@Param("name") String name);

// ✅ SAFE — Criteria. The query is a tree; literals become bind parameters.
cb.equal(root.get("customerName"), name);

// ✅ SAFE — JdbcClient with .param()
db.sql("select * from orders where customer_name = :name").param("name", name);
```

**The mechanism, and be able to state it:** the query is parsed into a plan *before* any value is
supplied. A parameter is a slot in that finished plan. **A value in a slot can never become
structure** — a `'` or a `--` or a `; drop table` is just characters in a string comparison, because
the parser has already finished and will not run again.

**This covers the overwhelming majority of JPA code.** The point of today is not that JPA is unsafe —
it is that the safety comes from *parameterisation*, not from *using an ORM*, and the moment you stop
parameterising it disappears entirely.

---

# Part 2 — ⭐ The six ways it goes wrong

### 1. Concatenated JPQL — JPQL injection is real

```java
public List<Order> search(String name) {
    return em.createQuery("select o from Order o where o.customerName = '" + name + "'",
                          Order.class).getResultList();     // ❌
}
```

Input: `' or '1'='1` → every order returned. **Authorization bypass with no SQL involved.**

JPQL is a query language with a parser; concatenating into it is injection into *that* language,
which Hibernate then translates faithfully into SQL. People assume "it's JPQL, not SQL, so it's
fine". It is not fine — and the JPQL surface even permits subqueries and `select` on other entities,
so an attacker can often read data from tables the endpoint never mentions.

### 2. Concatenated native queries — ordinary SQL injection

```java
@Query(value = "select * from orders where customer_name = '" + "?" + "'", nativeQuery = true)
```
```java
db.sql("select * from orders where name = '" + name + "'")     // ❌ Day 119b, verbatim
```

Full SQL injection. `nativeQuery = true` removes even JPQL's structural constraints — `UNION SELECT`
against any table, stacked statements if the driver allows them.

### 3. ⭐ `ORDER BY` — the one that catches careful people

```java
@Query("select o from Order o order by " + "#{#sort}")            // ❌
db.sql("select * from orders order by " + sortField + " " + direction)   // ❌
```

**You cannot bind an identifier.** `order by :column` does not work — a parameter is a *value*, and a
column name is *structure*. This is precisely why Day 108 and Day 152 both demanded an allow-list:
it is not a stylistic preference, it is the only available defence.

```java
private static final Map<String, String> SORTABLE = Map.of(
        "placedAt", "placed_at", "total", "total", "status", "status");   // ⭐ allow-list

String column = SORTABLE.get(requested);
if (column == null) throw new BadRequestException("unsupported sort field");
String dir = "desc".equalsIgnoreCase(direction) ? "desc" : "asc";          // ⭐ ternary, not input
```

**The same applies to table names, column names in dynamic pivots, and `LIMIT` when built as text.**
Anywhere you must concatenate structure, the value must come from a fixed map — never from the
request, never "sanitised".

### 4. ⭐ `LIKE` — safe from injection, vulnerable to denial of service

```java
@Query("select o from Order o where o.name like :pattern")
List<Order> search(@Param("pattern") String pattern);

repo.search("%" + userInput + "%");        // ⭐ bound — NOT injectable
```

That is genuinely not injectable. But two other problems live here:

- **Wildcard injection.** Input `%` matches everything, turning a search into a full table dump.
  Escape `%`, `_` and the escape character itself in user input:
  `input.replace("!", "!!").replace("%", "!%").replace("_", "!_")` with
  `like :pattern escape '!'`.
- ⭐ **Leading-wildcard cost.** `like '%foo%'` cannot use a B+ tree index (D-11), so it is a full
  table scan. On a large table, a handful of concurrent searches saturates the database — a denial of
  service through a feature that works perfectly. Use a trigram index, a full-text index, or a real
  search engine.

### 5. ⭐ SpEL in `@Query` — the trap Day 138 flagged

```java
@Query("select o from Order o where o.tenantId = ?#{principal.tenantId}")     // ✅ safe: from context
@Query("select o from Order o where o.name = ?#{#name}")                      // ⚠️ read on
```

Day 138's point, restated with its consequence: **the SpEL expression is evaluated *before* the query
is parsed, and its result is spliced into the query text.** So a SpEL expression fed from user input
escapes the prepared-statement boundary entirely — the very protection you thought you had.

**Rule: SpEL in `@Query` may reference the security context or configuration. Never user input.**

### 6. `@Filter` and dynamic multi-tenancy conditions

```java
@Filter(name = "tenantFilter", condition = "tenant_id = :tenantId")     // ✅ parameterised
```

Fine as written. Building that `condition` string from a request value is not — and because a filter
applies invisibly to every query on the entity, a flaw here is a cross-tenant data leak on **every
endpoint at once**. Day 118's row-level security is the sturdier answer.

---

# Part 3 — What an ORM cannot protect you from at all

**Injection is a general class, not a SQL topic.** Three that appear in Java services constantly:

| Class | Example | Defence |
|---|---|---|
| **JPQL/HQL** | above | parameterise |
| ⭐ **Deserialization** | Day 068 — `readObject` on untrusted bytes is RCE | never deserialize untrusted data |
| ⭐ **SpEL / expression** | Day 138, Log4Shell | `SimpleEvaluationContext`; never evaluate user input |
| **LDAP** | `(uid=" + user + ")` | escape per RFC 4515 |
| **Command** | `Runtime.exec("sh -c " + input)` | `ProcessBuilder` with an argument list |
| **Path traversal** | `new File(base, userPath)` → `../../etc/passwd` | canonicalise, then verify the prefix |
| **Header/log** | a `\n` in a logged value forges log lines | strip control characters (Day 151) |

**The unifying rule, from Day 119b:**

> **Never let data become code. Where the platform offers separation — bind parameters, argument
> arrays, structured logging — use it. Where it cannot (identifiers, file paths, class names), use a
> fixed allow-list.**

---

# Part 4 — Verifying it, rather than believing it

**Reading code is not a control.** Three that are:

```java
// 1. ArchUnit — ban the concatenation entirely
@Test void noStringConcatenationInQueries() {
    noClasses().that().resideInAPackage("..repository..")
        .should().callMethod(EntityManager.class, "createQuery", String.class)
        .check(classes);       // ⭐ force everything through @Query or a fragment
}
```

```java
// 2. A test with actual payloads
@ParameterizedTest
@ValueSource(strings = {"' or '1'='1", "'; drop table orders; --", "%", "_", " "})
void searchIsInjectionSafe(String payload) {
    assertThat(service.search(payload)).isEmpty();          // ⭐ no results, no exception, no damage
    assertThat(repo.count()).isEqualTo(EXPECTED);           // ⭐ table still there
}
```

3. **Static analysis in CI** — SpotBugs with find-sec-bugs flags `SQL_INJECTION_JPA` and
   `SQL_INJECTION_SPRING_JDBC` directly. Day 100A's supply-chain scanning belongs in the same
   pipeline stage.

**And the defence in depth that limits the damage when all of the above fails:**

- **The application's database user has the minimum privileges it needs.** No `DROP`, no `CREATE`, no
  access to other schemas, and separate read-only credentials for reporting. A successful injection
  against a least-privileged account reads what that endpoint could already read; against a superuser
  it owns the database.
- **Row-level security** (Day 118) so a leaked query still cannot cross tenants.
- **Query timeouts** (`javax.persistence.query.timeout`) so an injected expensive query cannot hold a
  connection indefinitely (Day 156).

---

## Common mistakes

| Mistake | Why it hurts |
|---|---|
| ⭐ "We use JPA so we're safe" | safety comes from parameterisation, not from the ORM |
| Concatenated JPQL | JPQL injection — full authorization bypass |
| Concatenated native SQL | classic injection, `UNION` to any table |
| Trying to bind a column name | impossible; the fallback must be an allow-list |
| Sanitising instead of allow-listing identifiers | blocklists always miss a case |
| Unescaped `%`/`_` in `LIKE` | full table dump via a search box |
| Leading-wildcard `LIKE` on a big table | index-less scan; DoS through a working feature |
| ⭐ SpEL in `@Query` fed from user input | evaluated before parsing; escapes parameter binding |
| Building `@Filter` conditions dynamically | cross-tenant leak on every query at once |
| Application DB user with DDL rights | injection escalates from read to destruction |
| No injection tests | the control is "someone remembered" |

## Interview questions

**Q: Does using JPA prevent SQL injection?**
No. It prevents it wherever queries are parameterised, which is most JPA code — but concatenated JPQL
is injectable into JPQL, concatenated native queries are ordinary SQL injection, and SpEL inside
`@Query` is evaluated before parsing so it bypasses binding entirely. The protection comes from
parameterisation, not from the ORM.

**Q: How do you handle a user-supplied sort column?**
An allow-list mapping request values to known column names, rejecting anything else. You cannot bind
an identifier — a parameter is a value and a column name is structure — so an allow-list is the only
available defence, not a stylistic choice.

**Q: Is `LIKE` with a bound pattern safe?**
Safe from injection, yes. But unescaped `%` or `_` in user input turns a search into a full table
dump, and a leading wildcard cannot use an index, so a search box becomes a denial-of-service vector
on a large table.

**Q: How do you verify this rather than assume it?**
An ArchUnit rule banning `createQuery(String)` outside approved places, parameterised tests firing
real payloads and asserting the table still exists, and find-sec-bugs in CI. Plus least-privilege
database credentials so a miss is a read rather than a `DROP`.

## Mini task

1. Write a deliberately vulnerable JPQL search. Exploit it with `' or '1'='1` and get every row.
2. Try to read a *different* entity through the same hole with a subquery. Note how far JPQL lets you
   go.
3. Fix it with `:named` parameters. Re-run every payload.
4. Build a sort parameter by concatenation. Inject something into `ORDER BY`. Replace with an
   allow-list.
5. Search with `%` as the input. Get every row. Add escaping with `escape '!'`.
6. `EXPLAIN` a `like '%foo%'` on a million rows. Confirm the sequential scan and time it.
7. Write a `@Query` with `?#{#name}` and pass a payload. **Confirm binding does not save you.**
8. Write the ArchUnit rule and the parameterised payload test. Make both pass.
9. Create a least-privilege database user. Re-run the vulnerable version and confirm `drop table`
   fails on permissions.

# 🚪 Exit questions

1. Why does parameterisation work? State the mechanism.
2. Give the five JPA constructs that are safe by default.
3. Give the six ways JPA code becomes injectable.
4. Why is concatenated JPQL dangerous even though it is not SQL?
5. Why can't you bind a column name, and what is the only defence?
6. Give the two problems with `LIKE`, and note which is not injection.
7. Why does SpEL in `@Query` bypass parameter binding?
8. Why is a flaw in a Hibernate `@Filter` condition especially serious?
9. Name four non-SQL injection classes and the defence for each.
10. State the unifying rule in one sentence.
11. Give three controls that verify safety rather than assume it.
12. What does least-privilege database access change about a successful injection?

## 🎙️ Articulation drill

Two minutes: **"Does an ORM protect you from injection?"**

Start by correcting the premise: parameterisation protects you, and an ORM makes parameterisation the
default — which is not the same thing. Then name where it leaks: concatenated JPQL, native queries,
and SpEL inside `@Query`, which evaluates before the query is parsed and so escapes binding entirely.
Give the identifier case properly — you cannot bind a column name, so a user-supplied sort field must
go through an allow-list. Close on verification and blast radius: an ArchUnit rule plus payload
tests, and a least-privilege database user so a miss reads data instead of dropping tables.

---

**Previous:** [Day 163](Day-163.md) · **Next:** [Day 164](Day-164.md) — ⭐ `@Transactional`
