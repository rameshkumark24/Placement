# Day 129B 🐳

**L-129B** · Docker primer II — Dockerfile, layers, build cache, volumes, port mapping

**Time:** 2 hrs · **Mode:** NEW · **Docker primer, 2 of 3**

> Writing your own image. Two things decide whether a Dockerfile is good: **layer ordering**, which
> determines whether your builds take 8 seconds or 4 minutes, and **what you leave out**, which
> determines your image size and attack surface.

---

# Part 1 — A Dockerfile, and why each line is where it is

```dockerfile
# ─── BUILD STAGE ────────────────────────────────────────────────
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /build

# ⭐ dependencies FIRST — they change rarely
COPY mvnw pom.xml ./
COPY .mvn .mvn
RUN ./mvnw dependency:go-offline -B

# source LAST — it changes on every commit
COPY src ./src
RUN ./mvnw package -DskipTests -B

# ─── RUNTIME STAGE ──────────────────────────────────────────────
FROM eclipse-temurin:21-jre-alpine          # ⭐ JRE, not JDK — no compiler in production
WORKDIR /app

RUN addgroup -S app && adduser -S app -G app    # ⭐ non-root (Day 078)
USER app

COPY --from=build /build/target/*.jar app.jar   # ⭐ ONLY the artifact crosses the stage boundary

EXPOSE 8080
ENV JAVA_OPTS="-XX:MaxRAMPercentage=75.0"
ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -jar app.jar"]
```

## ⭐ Layer caching — the single most important idea

**Every instruction creates a layer, and Docker caches them. A layer is reused only if the
instruction and everything before it are unchanged. Once one layer is invalidated, every layer after
it rebuilds.**

```dockerfile
# ❌ every source change re-downloads every dependency
COPY . .
RUN ./mvnw package

# ✅ dependencies cached independently of source
COPY pom.xml .
RUN ./mvnw dependency:go-offline      # ← cached until pom.xml changes
COPY src ./src
RUN ./mvnw package                    # ← only this reruns on a code change
```

**In practice: a 4-minute build becomes an 8-second build.** The rule is one sentence:

> **Order instructions from least likely to change to most likely to change.**

## Multi-stage builds

```dockerfile
FROM maven AS build      # JDK, Maven, source, the whole ~/.m2 cache — ~800 MB
…
FROM eclipse-temurin:21-jre-alpine    # ~180 MB
COPY --from=build /build/target/*.jar app.jar
```

**Only the final stage becomes the image.** The build tools, the source code, the dependency cache
and any secrets used during the build are all discarded.

That last point matters: **a secret used in an earlier stage is not in the final image**, whereas a
secret in a `RUN` in the same stage is in a layer forever — even if a later line deletes it. Layers
are immutable, so `RUN` adding a file and a later `RUN` removing it leaves the file in the earlier
layer, retrievable by anyone with the image.

```dockerfile
RUN --mount=type=secret,id=npmtoken npm install    # ⭐ BuildKit — never lands in a layer
```

## Base images

| Image | Size | Trade-off |
|---|---|---|
| `eclipse-temurin:21-jdk` | ~450 MB | Full Debian; every tool available |
| `eclipse-temurin:21-jre` | ~270 MB | No compiler ✅ |
| `eclipse-temurin:21-jre-alpine` | ~180 MB | musl libc ⚠️ |
| `gcr.io/distroless/java21` | ~230 MB | **No shell, no package manager** — minimal attack surface |

**Alpine's caveat is worth knowing:** it uses musl rather than glibc, which occasionally breaks
native libraries and has historically produced DNS-resolution quirks. Test rather than assume.

**Distroless is the strongest production choice** — no shell means no shell for an attacker either —
at the cost of being harder to debug (Day 129A's `docker debug`).

## `CMD` vs `ENTRYPOINT`

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]   # the executable — hard to override
CMD ["--spring.profiles.active=prod"]     # default ARGUMENTS — overridable at run time
```

**Use exec form (`["a","b"]`), not shell form.** Shell form runs your process as a child of
`/bin/sh`, so **`SIGTERM` goes to the shell and never reaches your JVM** — your shutdown hook never
runs and every stop is effectively a `SIGKILL` (Day 081). That is one of the most common and most
damaging Dockerfile mistakes, and it only shows up as data loss during deploys.

`exec java …` inside `sh -c` (as above) fixes it by *replacing* the shell process, which is why the
example is written that way when you need shell variable expansion.

## `.dockerignore`

```
target/
.git/
.env                ⭐
*.log
node_modules/
.idea/
```

**Without it, `COPY . .` sends your entire directory to the daemon** — including `.git`, build
output and, critically, `.env`. That is how secrets end up in images. It also makes every build
slower and invalidates the cache on any file change.

---

# Part 2 — ⭐ The JVM in a container

**A JVM before Java 10 read the *host's* memory and CPU, not the container's cgroup limits.** It
would size a heap for a 64 GB machine inside a 512 MB container and be OOM-killed immediately.

Modern JVMs are container-aware, but you must still configure them correctly:

```dockerfile
ENV JAVA_OPTS="-XX:MaxRAMPercentage=75.0 \
               -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp \
               -XX:+ExitOnOutOfMemoryError"
```

> **Use `-XX:MaxRAMPercentage`, not `-Xmx`.** A percentage adapts when the container limit changes;
> a hard-coded `-Xmx` has to be updated in two places and will eventually disagree with the limit.

**Why 75% and not 100%:** the heap is not the only memory the JVM uses. Metaspace, thread stacks
(~1 MB each — Day 064), the JIT code cache, direct byte buffers and GC structures all live outside
it (Day 071's RSS-versus-`-Xmx` point). Give the heap 75% and leave the rest for non-heap, or the
**kernel OOM killer takes the process with exit code 137** (Day 079) — with no Java exception, no
stack trace and nothing in your application logs.

`-XX:+HeapDumpOnOutOfMemoryError` is Day 076's rule, and in a container the path must be a mounted
volume or the dump dies with the container.

**Container-aware CPU matters too:** `--cpus=1.5` changes `availableProcessors()`, which sizes the
common ForkJoinPool (Day 060), GC threads and connection pools. A container limited to one CPU
running a parallel stream sized for 16 is a self-inflicted problem.

---

# Part 3 — Volumes

**Containers are ephemeral; volumes are not** (Day 129A).

```bash
docker volume create pgdata
docker run -v pgdata:/var/lib/postgresql/data postgres:16     # named volume ✅
docker run -v $(pwd)/src:/app/src myapp                        # bind mount — dev only
docker run --tmpfs /tmp myapp                                  # in-memory, never persisted
```

| Type | Managed by | Use for |
|---|---|---|
| **Named volume** | Docker | ✅ **Databases and any persistent state** |
| **Bind mount** | You (a host path) | ✅ Development live-reload · ⚠️ slow on macOS/Windows |
| **tmpfs** | Memory | Secrets and scratch that must not touch disk |

**Named volumes for data, bind mounts for source.** A bind mount for a database data directory
brings host permission problems and, on Docker Desktop, poor I/O performance because it crosses the
VM boundary.

```bash
docker volume ls
docker volume inspect pgdata
docker volume rm pgdata           # ⚠️ irreversible data loss
```

⚠️ **`docker compose down -v` deletes volumes.** It is the difference between "stop my stack" and
"delete my database", and people run it by muscle memory.

**Volumes are not backups.** A volume lives on one host's disk. Backing up a database means
`pg_dump` on a schedule, not "we have a volume".

---

# Part 4 — Ports, environment and health

```bash
docker run -p 8080:8080 myapp        # host:container (Day 129A)
docker run -p 127.0.0.1:8080:8080 myapp   # ⭐ bind to localhost only
docker run -P myapp                   # publish EXPOSEd ports to random host ports
```

⚠️ **`-p 8080:8080` binds to `0.0.0.0` — every interface.** On a cloud VM with a permissive security
group, that is a public database. **Bind to `127.0.0.1` unless you deliberately want it exposed**,
and remember that Docker writes its own iptables rules, which can bypass a host firewall (`ufw`) —
a genuinely surprising and frequently-exploited behaviour.

`EXPOSE` in a Dockerfile is **documentation only**; it publishes nothing.

## Configuration

```bash
docker run -e SPRING_PROFILES_ACTIVE=prod --env-file .env myapp
```

**Environment variables are the standard mechanism** (twelve-factor), and they have a real limit:

> ⚠️ **Environment variables are visible in `docker inspect`, in `/proc/<pid>/environ`, and often in
> logs and crash reports.** They are acceptable for configuration and marginal for secrets.

For real secrets, use Docker secrets (mounted as files under `/run/secrets`), a secrets manager, or
Kubernetes secrets mounted as files. **A file the process reads is better than an environment
variable it inherits**, because a file can have permissions and does not appear in every child
process's environment (Day 115b).

## Healthcheck

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD wget -qO- http://localhost:8080/actuator/health/readiness || exit 1
```

`--start-period` is the one that matters for the JVM: a slow-starting application otherwise fails
its first checks and looks unhealthy. This is Day 128's readiness probe, and the same rule applies —
**readiness, not liveness**, and do not put a dependency in a check that triggers a restart.

## Image security

```bash
docker scout cves myapp:1.4.2
trivy image myapp:1.4.2                    # Day 100A
```

The checklist:

```
✅ A minimal base image (JRE, alpine or distroless)
✅ Run as a non-root USER                        ← Day 078's principle, in a Dockerfile
✅ Pin base image versions, ideally by digest    ← Day 129A
✅ Multi-stage, so build tools never ship
✅ No secrets in layers — use BuildKit mounts
✅ .dockerignore excluding .git and .env
✅ Scan in CI and fail on high/critical
✅ Read-only root filesystem where possible: --read-only --tmpfs /tmp
```

**Running as root inside a container is the default and is wrong.** A container escape then starts
from root, and even without an escape, a bind-mounted directory is writable as root on the host.
`USER app` costs two lines.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| `COPY . .` before dependency installation | Every build re-downloads everything |
| No `.dockerignore` | `.git` and `.env` in the image; slow builds |
| Secrets in a `RUN` | Present in a layer forever, even if deleted later |
| Shell-form `ENTRYPOINT` | **`SIGTERM` never reaches the JVM** — no graceful shutdown |
| JDK base image in production | Larger image, compiler and tools shipped |
| Running as root | Escalation on escape; root-owned files on bind mounts |
| `-Xmx` instead of `MaxRAMPercentage` | Drifts from the container limit |
| Heap at 100% of the limit | OOM-killed with exit 137 and no Java error |
| Bind mount for database data | Permission problems; slow on Docker Desktop |
| `-p` binding to `0.0.0.0` on a cloud host | A publicly exposed database |
| Expecting `EXPOSE` to publish | It documents; it does not publish |
| Secrets in environment variables | Visible in `inspect`, `/proc`, and crash reports |
| No `--start-period` | A slow-starting JVM is marked unhealthy |
| `compose down -v` from habit | ⚠️ Deletes your data |
| Treating volumes as backups | One host's disk is not a backup |

---

## Interview questions

**Q: What is layer caching and why does instruction order matter?**
Each instruction is a cached layer, reused only if it and everything before it are unchanged.
Invalidating one layer rebuilds all subsequent ones — so ordering from least- to most-frequently
changed (dependencies before source) turns a four-minute build into seconds.

**Q: What is a multi-stage build for?**
To keep build tooling, source and any build-time secrets out of the final image — only what you
explicitly `COPY --from` crosses the boundary. A JDK-and-Maven image of 800 MB becomes a 180 MB JRE
image.

**Q: Why can't you delete a secret in a later `RUN`?**
Layers are immutable. A file added in one layer remains in that layer even if a later layer removes
it, and anyone with the image can extract it. Use BuildKit secret mounts or a separate build stage.

**Q: Why must `ENTRYPOINT` use exec form?**
Shell form runs your process as a child of `/bin/sh`, so `SIGTERM` goes to the shell and never
reaches the JVM — shutdown hooks do not run, and every stop becomes an effective `SIGKILL` after the
grace period.

**Q: How do you size the JVM heap in a container?**
`-XX:MaxRAMPercentage=75`, so it tracks the cgroup limit automatically. Not 100%, because metaspace,
thread stacks, the code cache and direct buffers live outside the heap — exceeding the limit gets the
process OOM-killed with exit 137 and no Java-level error.

**Q: Named volume or bind mount?**
Named volumes for persistent data — Docker manages them, they avoid host permission problems and they
perform properly. Bind mounts for source during development, where live reload is the point.

**Q: What is wrong with secrets in environment variables?**
They are visible in `docker inspect`, in `/proc/<pid>/environ`, and often in logs and crash reports,
and they are inherited by child processes. A mounted secret file with restricted permissions is
better.

**Q: Why run as a non-root user?**
A container escape or a vulnerability starts from root otherwise, and bind-mounted files are written
as root on the host. It costs two lines in the Dockerfile.

---

## Mini task

1. Write a Dockerfile with `COPY . .` before dependency resolution. Time a rebuild after a one-line
   source change. Reorder it and time again.
2. Convert it to multi-stage and compare the final image sizes with `docker images`.
3. Add a secret in a `RUN`, delete it in the next `RUN`, then extract it from the image with
   `docker save` and `tar`. Confirm it is still there.
4. Use shell-form `ENTRYPOINT` with an application that logs on shutdown. `docker stop` it and
   confirm the hook never runs. Switch to exec form and confirm it does.
5. Run with `-m 512m` and `-Xmx512m`, load it, and get the container OOM-killed. Switch to
   `MaxRAMPercentage=75` and confirm it survives.
6. Print `Runtime.availableProcessors()` inside a container with and without `--cpus=1`.
7. Add a non-root `USER` and confirm with `docker exec whoami`. Try to write to `/` and observe the
   failure.
8. Run Postgres with a named volume. Remove the container, start a new one on the same volume, and
   confirm the data survives. Then run `docker volume rm` and confirm it does not.
9. Bind `-p 8080:8080` and check what it listens on with `ss -tlnp`. Change to `127.0.0.1:8080:8080`
   and compare.
10. Add a `HEALTHCHECK` with `--start-period` and watch the status transition in `docker ps`.
11. Run `trivy image` against your image and against `openjdk:8`. Compare the findings.
12. Build with `--read-only --tmpfs /tmp` and fix whatever breaks.

---

# 🚪 Exit questions

1. State the layer-caching rule and the ordering principle that follows.
2. What does a multi-stage build discard, and why does that matter for secrets?
3. Why does deleting a file in a later `RUN` not remove it from the image?
4. Give the four base-image options with their trade-offs.
5. Why must `ENTRYPOINT` be exec form? What exactly breaks?
6. Difference between `CMD` and `ENTRYPOINT`?
7. What belongs in `.dockerignore` and why does `.env` matter most?
8. Why `MaxRAMPercentage` rather than `-Xmx`, and why not 100%?
9. Name four things that use memory outside the heap.
10. Named volume versus bind mount — which for what?
11. What does `-p 8080:8080` bind to, and what is the cloud-host risk?
12. What does `EXPOSE` actually do?
13. Why are environment variables weak for secrets?

## 🎙️ Articulation drill

Two minutes: **"How would you containerise a Java service for production?"**

Multi-stage with dependencies cached before source; a JRE or distroless runtime; non-root user;
`MaxRAMPercentage` with heap-dump flags; exec-form entrypoint so `SIGTERM` reaches the JVM;
`.dockerignore` excluding `.git` and `.env`; pinned base image; a readiness `HEALTHCHECK` with a
start period; and image scanning in CI. Each item exists for a specific failure — say the failure, not
just the flag.

---

**Previous:** [Day 129A](Day-129A.md) · **Next:** [Day 129C](Day-129C.md) — compose, and the Stage 3 gate

> Tomorrow: `docker compose` and the Postgres + Redis stack that becomes your development environment
> for Stages 4 through 7 — then the Stage 3 exit gate.
