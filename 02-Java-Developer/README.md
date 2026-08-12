# 02 — Java Developer

| File | Contents |
|---|---|
| [Java-Vibe-Coding-Cheatsheet.md](Java-Vibe-Coding-Cheatsheet.md) | **Build guide.** Stack, layering, Spring/JPA traps, API safety, security, testing, delivery |
| [Core/](Core/) | Interview prep — see below |
| [Spring-Boot/Annotations-Reference.md](Spring-Boot/Annotations-Reference.md) | Saved reference |

## Interview material

- [Basics](Core/Basics.md)
- [OOPs](Core/OOPs.md)
- [HashMap Internals](Core/HashMap-Internals.md)
- [Interview Questions](Core/Interview-Questions.md) — fresher-level Java backend
- [Why Spring Boot](../06-Common/HR-Interview/Why-Spring-Boot.md) — the HR-round answer

> Universal build/security/API rules live in [`03-Web-Developer`](../03-Web-Developer/) phases 04–08.
> SQL notes: [`06-Common/SQL`](../06-Common/SQL/).

## Default stack

Java 21 · Spring Boot 3 · Gradle (Kotlin DSL) · Spring Data JPA · Flyway · Postgres ·
Spring Security + JWT · Testcontainers · Actuator + Micrometer

## The three that cause the most damage

1. **N+1 queries** from lazy loading in a loop → use `JOIN FETCH`.
2. **Returning a JPA entity from a controller** → leaks fields, lazy-init exceptions, JSON recursion.
3. **No timeouts on `RestClient`/`WebClient`** → one slow dependency exhausts the thread pool.

All three: [Java-specific traps](Java-Vibe-Coding-Cheatsheet.md#3-java-specific-traps-in-ai-generated-code)
