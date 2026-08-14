# Day 119b

**L-119b** · SQL injection in depth — why concatenation is fatal, prepared statements as the fix

**Time:** 2 hrs · **Mode:** NEW

> SQL injection is over twenty-five years old, is completely solved, and is still in the OWASP Top 10
> — because the fix requires understanding *why* it works, not just which method to call. Today you
> will exploit it, then fix it, then find the cases where the standard fix does not apply.

---

# Part 1 — Why it works

```java
String sql = "SELECT * FROM users WHERE email = '" + email + "' AND active = true";
```

**The vulnerability is not "user input reached the database". It is that user input became
*code*.** The database receives one string and parses it into a query — it has no way to know which
characters you intended as data and which as syntax.

```
email = ram@example.com
→ SELECT * FROM users WHERE email = 'ram@example.com' AND active = true      ✅

email = ' OR '1'='1
→ SELECT * FROM users WHERE email = '' OR '1'='1' AND active = true          💀

email = ' OR 1=1 --
→ SELECT * FROM users WHERE email = '' OR 1=1 --' AND active = true          💀
                                              ↑ everything after -- is a comment
```

The attacker did not "escape a quote". They **changed the parse tree**: a query with one condition
became a query with two, and the `--` deleted the rest of your logic.

## What an attacker can actually do

```sql
'; DROP TABLE users; --                        -- destruction (rarely the goal)
' UNION SELECT email, password_hash, NULL FROM users --     -- data theft ⭐ the real goal
' OR 1=1 --                                    -- authentication bypass
'; UPDATE users SET role='ADMIN' WHERE id=17; --            -- privilege escalation
' AND (SELECT COUNT(*) FROM information_schema.tables) > 0 --  -- schema discovery
'; COPY users TO '/tmp/out.csv'; --            -- file write (with sufficient privilege)
'; SELECT pg_sleep(10); --                     -- blind / time-based extraction
```

**`UNION SELECT` is the one that matters** — it exfiltrates entire tables through an endpoint that was
meant to return one row.

### Blind SQL injection

Even with no visible output, data leaks one bit at a time:

```sql
-- boolean-based: the page renders differently depending on the answer
' AND SUBSTRING((SELECT password_hash FROM users WHERE id=1),1,1) = 'a' --

-- time-based: works with NO output at all
' AND (SELECT CASE WHEN (SUBSTRING(password_hash,1,1)='a')
                   THEN pg_sleep(3) ELSE pg_sleep(0) END FROM users WHERE id=1) --
```

**A ten-second difference in response time is a data channel.** `sqlmap` automates this and will
extract an entire database through a single boolean or timing oracle, given time. "The error is not
displayed" is not a mitigation.

---

# Part 2 — Prepared statements, and why they actually fix it

```java
String sql = "SELECT * FROM users WHERE email = ? AND active = true";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
    ps.setString(1, email);          // ← DATA, never parsed as SQL
    ResultSet rs = ps.executeQuery();
}
```

**The mechanism, which is the part to be able to explain:**

```
1. The SQL TEMPLATE is sent to the database first — with ? placeholders.
2. The database PARSES it and builds an execution plan. The parse tree is now FIXED.
3. Parameters are sent SEPARATELY, over the protocol, as typed values.
4. They are bound into the already-parsed plan. They CANNOT become syntax.
```

> **Prepared statements do not escape input. They separate code from data at the protocol level, so
> there is nothing to escape.** No amount of quotes, comments or semicolons in a parameter can change
> a plan that was already built.

That is why the fix is complete rather than best-effort — and why it is categorically better than
escaping, which is a deny-list (Day 105) racing against every encoding quirk.

**Note the secondary benefit:** the plan is reusable, so repeated executions skip parsing and
planning (Day 113's D-12). Prepared statements are usually *faster* as well as safe.

## ⚠️ What cannot be parameterised

**Only values can be bound. Identifiers and syntax cannot.**

```java
// ❌ these do NOT work — the driver will reject them or bind a literal string
"SELECT * FROM ?"                  // table name
"SELECT ? FROM users"              // column name — binds the STRING, not the column
"ORDER BY ?"                       // sort column — same
"ORDER BY created_at ?"            // ASC/DESC
"WHERE status IN (?)"              // ⚠️ a list — see below
```

**This is exactly why Day 108 insisted on allow-lists for filter and sort columns.** The one part of
a dynamic query that must be concatenated is the identifier — so it must come from a closed map, never
from the request:

```java
private static final Map<String, String> SORTABLE = Map.of(
    "created_at", "created_at", "total", "total_cents");

String column = SORTABLE.get(userField);
if (column == null) throw new BadRequestException("cannot sort by " + userField);
String direction = "desc".equalsIgnoreCase(userDir) ? "DESC" : "ASC";   // closed set

String sql = "SELECT * FROM orders WHERE tenant_id = ? ORDER BY " + column + " " + direction;
```

**Dynamic `IN` lists** need one placeholder per element:

```java
String placeholders = ids.stream().map(x -> "?").collect(joining(","));
String sql = "SELECT * FROM orders WHERE id IN (" + placeholders + ")";
for (int i = 0; i < ids.size(); i++) ps.setLong(i + 1, ids.get(i));

// PostgreSQL alternative — one parameter, an array
"SELECT * FROM orders WHERE id = ANY(?)"      → ps.setArray(1, conn.createArrayOf("bigint", ids))
```

⚠️ **Bound the list size.** A 100,000-element `IN` clause is a denial of service and destroys plan
caching.

## ORMs and JPA — safe by default, unsafe on request

```java
// ✅ safe — parameterised
repo.findByEmail(email);
em.createQuery("SELECT u FROM User u WHERE u.email = :email").setParameter("email", email);
@Query("SELECT u FROM User u WHERE u.email = :email")

// 💀 unsafe — concatenation, in JPQL or native SQL
em.createQuery("SELECT u FROM User u WHERE u.email = '" + email + "'");
em.createNativeQuery("SELECT * FROM users WHERE email = '" + email + "'");
@Query(value = "SELECT * FROM users WHERE " + condition, nativeQuery = true)
```

**JPQL is injectable too.** It is a query language that gets parsed, so concatenating into it has the
same failure mode as raw SQL. Using an ORM is not protection — using it *correctly* is.

**Spring Data `@Query` with SpEL** deserves a specific warning: `?#{...}` expressions are evaluated
before parameter binding, so user input reaching a SpEL expression is an expression-injection
vulnerability that can reach beyond SQL.

## Stored procedures are not automatically safe

```sql
CREATE PROCEDURE find_user(email TEXT) AS $$
BEGIN
    EXECUTE 'SELECT * FROM users WHERE email = ''' || email || '''';   -- 💀 injection INSIDE
END $$;
```

Dynamic SQL built inside a procedure is exactly as vulnerable. Use `USING` (or `sp_executesql` with
parameters in SQL Server) and `quote_ident`/`quote_literal` for identifiers.

---

# Part 3 — Defence in depth

Parameterised queries are the fix. These reduce the blast radius when something is missed:

**1. Least privilege for the database user.** The application should not be a superuser.

```sql
CREATE USER app_user WITH PASSWORD '…';
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
REVOKE CREATE ON SCHEMA public FROM app_user;          -- no DDL
-- and definitely no COPY, no superuser, no file access
```

**`'; DROP TABLE users; --` fails on a permission error if the application user cannot `DROP`.** A
read-heavy service can use a read-only connection for its read paths, which makes `UPDATE`-style
injection impotent.

**2. Input validation** (Day 105) — an allow-list on an id field rejects `' OR 1=1 --` before it ever
reaches the query layer. Not the primary control; a good backstop.

**3. Disable multi-statement execution.** Many drivers do not permit `;` chaining by default — keep
it that way (`allowMultiQueries=false` in MySQL Connector/J is the default and should stay).

**4. Generic error messages** (Day 104). Verbose database errors turn blind injection into
error-based injection, which is far faster to exploit.

**5. Row-level security** (Day 118). Even a successful injection returns only the current tenant's
rows.

**6. Monitoring.** Alert on database errors from the application, on unusual query volumes, and on
queries with unusual shapes. A `sqlmap` run is thousands of malformed queries — visible if anyone is
looking.

## Finding it in a codebase

```bash
# concatenation adjacent to SQL keywords
grep -rn "\" *+" --include=*.java | grep -iE "select|insert|update|delete|where|from"

# the danger methods
grep -rn "createNativeQuery\|createQuery\|Statement\.execute\|nativeQuery *= *true" --include=*.java

# String.format into SQL
grep -rn "String.format" --include=*.java | grep -iE "select|where"
```

Then automate it — **static analysis catches this class reliably**, which is why it should never
reach production:

- **SpotBugs + find-sec-bugs** — `SQL_INJECTION_JDBC`, `SQL_INJECTION_JPA`
- **SonarQube** — rule S3649
- **Semgrep** — `java.lang.security.audit.sqli`
- **CodeQL** — taint tracking from request parameters to query execution

Add one to CI (Day 100A) and this vulnerability class stops being possible to merge.

## The other injections — same principle

The reasoning generalises, and this is the sentence worth carrying:

> **Any time user input is concatenated into something that gets *parsed*, you have an injection
> vulnerability.**

| Type | Context | Fix |
|---|---|---|
| SQL | Query | Parameterised statements |
| **NoSQL** | Mongo query documents — `{"$ne": null}` as a password | Type checking; never pass raw objects |
| Command | `Runtime.exec("ping " + host)` | `ProcessBuilder` with an argument array; never a shell |
| LDAP | Directory filters | Escape per RFC 4515 |
| XPath / XXE | XML parsing | Disable external entities |
| Template / SSTI | `${…}` in a template engine | Never build templates from user input |
| **Log** | CR/LF in log lines (Day 073A) | Strip control characters |
| Header | CR/LF in a response header | Framework rejection; validate |
| **Expression** | SpEL, OGNL — Log4Shell (Day 073A) | Never evaluate user input |

**Command injection is the one worth writing out**, because the fix is structural in the same way:

```java
// 💀 a shell parses this string — ; | && $() are all live
Runtime.getRuntime().exec("ping -c 1 " + userHost);

// ✅ an argument ARRAY — no shell, no parsing, nothing to inject into
new ProcessBuilder("ping", "-c", "1", userHost).start();
```

Same principle as a prepared statement: separate the command from its arguments so the arguments
cannot become syntax.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| String concatenation into SQL | Injection |
| Escaping instead of parameterising | A deny-list racing every encoding quirk |
| Believing an ORM makes you safe | JPQL and native queries concatenate just as badly |
| Concatenating into JPQL | Injectable query language |
| SpEL in `@Query` with user input | Expression injection beyond SQL |
| Dynamic SQL inside a stored procedure | Injection moved, not removed |
| Column or table names from the request | The one thing that cannot be parameterised |
| Unbounded `IN` list | Denial of service; plan cache destroyed |
| Application connecting as a superuser | `DROP TABLE` succeeds |
| Verbose database errors in responses | Error-based injection |
| Assuming no visible output means no leak | Boolean and timing oracles extract everything |
| No static analysis in CI | A solved vulnerability class keeps shipping |
| `Runtime.exec` with a concatenated string | Command injection |

---

## Interview questions

**Q: Why does SQL injection work?**
Because concatenation makes user input part of the SQL *text*, which the database parses. The
attacker changes the parse tree — one condition becomes two, or `--` deletes the rest of the query.
The database cannot distinguish intended syntax from injected syntax.

**Q: Why do prepared statements fix it, and how are they different from escaping?**
The template is parsed and planned *before* parameters are sent, and parameters travel separately as
typed values bound into the fixed plan. There is nothing to escape, because data never enters the
parser. Escaping is a deny-list; parameterisation is structural.

**Q: What cannot be parameterised?**
Identifiers and syntax — table names, column names, `ORDER BY` direction, and the structure of an
`IN` list. Those must come from an allow-list, which is why dynamic sorting and filtering need a
closed map from API field names to columns.

**Q: Does using an ORM protect you?**
Only if used correctly. Derived queries and named parameters are safe; concatenating into JPQL or a
native query is exactly as vulnerable, and SpEL in a `@Query` adds expression injection on top.

**Q: What is blind SQL injection?**
Extraction with no visible output — a boolean oracle (the page differs) or a timing oracle
(`pg_sleep`). A response-time difference is a data channel, so hiding the error message is not a
mitigation; `sqlmap` automates full extraction from either.

**Q: What defences do you keep even with parameterised queries?**
Least-privilege database users so DDL and file access fail, input validation as a backstop, generic
error messages, row-level security, no multi-statement execution, and static analysis in CI.

**Q: State the general principle.**
Any time user input is concatenated into something that will be parsed — SQL, a shell command, LDAP,
XPath, a template, an expression language, a log line — you have an injection vulnerability. The fix
is always the same shape: separate the code from the data structurally.

---

## Mini task

1. Build a vulnerable login with concatenation. Bypass it with `' OR 1=1 --`.
2. Extract the user table with `' UNION SELECT email, password_hash, NULL FROM users --`. Match the
   column count and types first.
3. Build a blind version with no output. Extract the first three characters of a hash using a
   boolean oracle, then using `pg_sleep`.
4. Run `sqlmap` against your own endpoint and watch it enumerate the schema.
5. Fix it with a prepared statement. Re-run every payload and confirm all fail.
6. Enable statement logging and observe the difference: the concatenated version logs one string; the
   prepared version logs a template plus separate parameters.
7. Build dynamic sorting with an allow-list. Then remove the allow-list and inject through the
   `ORDER BY` clause.
8. Create a least-privilege database user with no DDL. Attempt `'; DROP TABLE users; --` and observe
   the permission error.
9. Write a dynamic `IN` clause safely with generated placeholders, then with `= ANY(?)`.
10. Build command injection with `Runtime.exec("ping " + host)` and `host = "8.8.8.8; cat /etc/passwd"`.
    Fix it with `ProcessBuilder`.
11. Add find-sec-bugs or Semgrep to a project and confirm it flags the vulnerable version.

---

# 🚪 Exit questions

1. State precisely why SQL injection works — in terms of parsing, not "user input".
2. Explain the four steps of a prepared statement and why escaping is categorically weaker.
3. What is the secondary performance benefit?
4. List five things that cannot be parameterised.
5. How do you handle dynamic column names and directions safely?
6. How do you build a safe dynamic `IN` list, and what must you bound?
7. Is JPQL injectable? Is a stored procedure automatically safe?
8. What is blind SQL injection, and why is hiding errors not a mitigation?
9. Give five defence-in-depth measures and what each limits.
10. What does a least-privilege database user prevent specifically?
11. State the general injection principle in one sentence.
12. Give the structural fix for command injection and why it is the same idea.

## 🎙️ Articulation drill

Two minutes: **"Explain SQL injection and how you prevent it."**

Explain it as a *parsing* problem — input becomes syntax and changes the parse tree — then prepared
statements as protocol-level separation rather than escaping, then what cannot be parameterised and
the allow-list that covers it. Close with defence in depth and the general principle, because
generalising to command, LDAP and expression injection shows you understand the mechanism rather than
one API call.

---

**Previous:** [Day 119](Day-119.md) · **Next:** [Day 120](Day-120.md) — caching theory

> **Security block complete: Days 111–119b.** Next: caching and rate limiting — performance work,
> with two of the most-asked system-design topics. Plus **D-14**, isolation levels and the anomalies
> they permit.
