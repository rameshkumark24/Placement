# Day 129A 🐳

**L-129A** · Docker primer I — why containers, images vs containers, `run`/`ps`/`logs`/`exec`

**Time:** 2 hrs · **Mode:** NEW · **Docker primer, 1 of 3**

> Three days to give you the containerised development environment used for Stages 4 through 7 — and
> enough understanding of containers to not be mystified by them.
>
> The first thing to fix: **a container is not a lightweight virtual machine.** That analogy is
> where most misconceptions start.

---

# Part 1 — What a container actually is

```
VIRTUAL MACHINE                          CONTAINER
┌─────────────────────────┐              ┌─────────────────────────┐
│ App │ App │ App         │              │ App │ App │ App         │
│ Bins/libs per VM        │              │ Bins/libs per container │
│ ⭐ GUEST OS (full kernel)│              │        (no OS)          │
├─────────────────────────┤              ├─────────────────────────┤
│      Hypervisor         │              │   Container runtime     │
├─────────────────────────┤              ├─────────────────────────┤
│      Host OS + kernel   │              │ ⭐ SHARED HOST KERNEL    │
└─────────────────────────┘              └─────────────────────────┘
   GBs · boots in minutes                  MBs · starts in ms
```

> **A container is a Linux process with restricted visibility and bounded resources. There is no
> guest operating system.**

It is built from three kernel features, and knowing them removes the magic (Day 023's B-01 and Day
081):

| Feature | Provides |
|---|---|
| **Namespaces** | *Isolation of view* — PID, network, mount, user, UTS, IPC |
| **cgroups** | *Resource limits* — CPU, memory, I/O |
| **Union filesystem** | *Layered images* — copy-on-write, shared layers |

**Namespaces are why `ps` inside a container shows only its own processes** — your process really is
PID 1 in its PID namespace, while the host sees it as PID 48231. Nothing is virtualised; the view is
restricted.

**cgroups are why a container's memory limit is real** — and why the JVM must be told about it
(Day 129B).

Two consequences that follow immediately, and both matter:

**The kernel is shared, so container isolation is weaker than a VM's.** A kernel vulnerability can
cross the boundary. This is why untrusted multi-tenant workloads use VMs, gVisor or Firecracker
rather than plain containers.

**You cannot run a Linux container on a Windows kernel.** Docker Desktop runs a Linux VM
transparently — which is why the first `docker run` on Windows or macOS is slower than you expect,
and why bind-mount file I/O is slow there.

## What containers actually solve

```
❌ "Works on my machine"     — the image contains the exact runtime, libraries and config
❌ Dependency conflicts      — two services can need different Java versions on one host
❌ Slow environment setup    — `docker compose up` instead of a page of instructions
❌ Dev/prod divergence       — the same image runs in both
✅ Density                    — hundreds per host instead of dozens of VMs
✅ Immutable deploys          — you ship an artifact, not a sequence of steps
```

**The immutability point is the one that changes how you work.** A container image is built once and
run everywhere unchanged. You do not patch a running container — you build a new image and replace
it. That is what makes rollback a matter of running the previous tag.

---

# Part 2 — Images vs containers

**The distinction everything else depends on:**

```
IMAGE       a read-only template — layered, immutable, shareable      ≈ a class
CONTAINER   a running instance with a writable layer on top          ≈ an object
```

```
                            ┌──────────────────────┐
Container (running)         │ writable layer  ⚠️   │  ← changes here are LOST on removal
                            ├──────────────────────┤
                            │ COPY app.jar         │  ┐
Image (read-only layers)    │ RUN apt-get install  │  │ SHARED between all containers
                            │ FROM eclipse-temurin │  ┘ from this image
                            └──────────────────────┘
```

**Every instruction in a Dockerfile creates a layer**, and layers are content-addressed by hash —
the same idea as Git objects (Day 084). Ten containers from one image share every read-only layer and
have their own thin writable layer, which is why starting a container is milliseconds and costs
almost no disk.

> ⚠️ **Anything written inside a container is lost when it is removed.** That is not a bug — it is
> the model. Persistent data goes in a **volume** (Day 129B).

---

# Part 3 — The commands

## Running

```bash
docker run hello-world                       # pull if needed, create, start
docker run -d --name web -p 8080:80 nginx    # -d detached, -p HOST:CONTAINER
docker run -it ubuntu bash                   # -it interactive terminal
docker run --rm alpine echo hi               # ⭐ --rm: delete on exit
docker run -e DB_URL=... myapp               # environment variable
docker run --env-file .env myapp
docker run -m 512m --cpus=1.5 myapp          # ⭐ cgroup limits
docker run -v mydata:/var/lib/postgresql/data postgres:16
```

**`-p 8080:80` is host:container**, and getting it backwards is the most common beginner error.
Without `-p`, the container's ports are unreachable from the host — the network namespace is
isolated by default.

**`--rm` should be your habit** for anything transient. Stopped containers otherwise accumulate
silently.

## Inspecting

```bash
docker ps                       # running
docker ps -a                    # ⭐ including stopped — where "disappeared" containers are
docker logs web                 # stdout/stderr (Day 127 — this is WHY you log to stdout)
docker logs -f --tail 100 web
docker inspect web              # everything, as JSON
docker stats                    # live CPU/memory per container
docker top web                  # processes inside
docker port web
```

**`docker logs` is the payoff for Day 127's "log to stdout".** The container runtime captures the
process's stdout, so logs work with no file configuration and are collected identically everywhere.

## Getting inside

```bash
docker exec -it web bash        # ⭐ a shell in a RUNNING container
docker exec web ls /app
docker exec -u root -it web sh  # as root, when the image runs as a non-root user
```

**`exec` is for debugging, never for changing anything that matters.** Any fix you apply inside a
running container is lost on the next deploy and exists nowhere in source control. **If you needed
to change it, change the Dockerfile.**

⚠️ Minimal images (`alpine`, `distroless`) may have no shell at all — that is deliberate hardening,
and the debugging answer is `docker debug` or an ephemeral sidecar rather than adding a shell to
production images.

## Lifecycle and cleanup

```bash
docker stop web        # SIGTERM, then SIGKILL after 10 s   ⭐ Day 081
docker kill web        # SIGKILL immediately
docker start web / restart web
docker rm web / docker rm -f web

docker images
docker rmi nginx
docker system df                 # ⭐ what is using disk
docker system prune              # remove stopped containers, unused networks, dangling images
docker system prune -a --volumes # ⚠️ ALSO removes volumes — data loss
```

**`docker stop` sends `SIGTERM` and waits 10 seconds before `SIGKILL`** (Day 081). So your
application's shutdown hook must complete within that window, or use `--time` to extend it. This is
exactly the Kubernetes grace-period relationship, one level down.

**Disk exhaustion from unused images is a real and common incident.** `docker system df` shows the
damage; schedule a prune.

## Registries and tags

```bash
docker pull postgres:16.3
docker tag myapp:latest registry.acme.com/myapp:1.4.2
docker push registry.acme.com/myapp:1.4.2
```

```
registry.acme.com/team/myapp:1.4.2
└─── registry ───┘└─repo─┘└─tag─┘
```

> ⚠️ **Never use `:latest` in production.** It is a mutable pointer — the same command pulls
> different bytes tomorrow, which destroys reproducibility (Day 100A) and makes rollback ambiguous.
> **Pin a version tag, or a digest:**

```bash
docker pull postgres@sha256:1f3d9a...          # ⭐ immutable, content-addressed
```

The digest form is the same content-addressing argument as pinning a GitHub Action to a SHA
(Day 100A) and as Git's object model (Day 084).

---

# Part 4 — Networking, briefly

```bash
docker network ls
docker network create appnet
docker run -d --name db --network appnet postgres:16
docker run -d --name api --network appnet -p 8080:8080 myapp
```

**On a user-defined network, containers reach each other by name.** Inside `api`, the database is at
`db:5432` — Docker runs an embedded DNS server that resolves container names. That is the whole
mechanism behind Day 129C's compose file.

Two rules worth fixing now:

**`localhost` inside a container means *that container*.** Pointing your application at
`localhost:5432` will not find a database in another container — use the container name.

**Use `host.docker.internal`** to reach a service running on the host machine from inside a
container (Docker Desktop; on Linux add `--add-host=host.docker.internal:host-gateway`).

The default `bridge` network does *not* provide name resolution — always create a user-defined
network, which is what compose does for you automatically.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| Thinking of a container as a VM | Wrong mental model for isolation, storage and lifecycle |
| Expecting data to persist | The writable layer is deleted with the container |
| `-p container:host` reversed | Nothing is reachable |
| No `-p` at all | The port is isolated by design |
| `localhost` between containers | Points at the container itself |
| Fixing things with `exec` | Lost on the next deploy; not in source control |
| `:latest` in production | Non-reproducible; ambiguous rollback |
| Never pruning | Disk exhaustion |
| `prune -a --volumes` carelessly | ⚠️ Data loss |
| Assuming `docker stop` is instant | 10 seconds of `SIGTERM` first |
| Trusting container isolation for untrusted code | The kernel is shared |

---

## Interview questions

**Q: What is a container?**
A Linux process with a restricted view and bounded resources — built from namespaces for isolation,
cgroups for limits, and a union filesystem for layered images. There is no guest OS; the host kernel
is shared.

**Q: Container versus VM?**
A VM virtualises hardware and runs a full guest kernel — gigabytes, boots in minutes, strong
isolation. A container shares the host kernel — megabytes, starts in milliseconds, weaker isolation.
That weaker isolation is why untrusted multi-tenant workloads still use VMs or sandboxed runtimes.

**Q: Image versus container?**
An image is an immutable, layered, read-only template; a container is a running instance with a thin
writable layer on top. Class and object. Data in the writable layer is lost when the container is
removed.

**Q: Why is starting a container so fast?**
There is no OS to boot — it is a process start, plus creating namespaces and a copy-on-write layer.
The image layers are shared and already on disk.

**Q: Why should you not use `:latest`?**
It is a mutable tag, so the same command can pull different bytes over time. That breaks
reproducibility and makes rollback ambiguous. Pin a version or, better, a digest.

**Q: How do containers talk to each other?**
On a user-defined network they resolve each other by container name through Docker's embedded DNS.
`localhost` inside a container refers to that container, not the host or a sibling.

**Q: Why log to stdout in a container?**
The runtime captures it, so `docker logs` and every platform collector work with no file
configuration — and files inside a container are lost on restart (Day 127).

---

## Mini task

1. Install Docker. Run `docker run --rm hello-world` and read what it reports.
2. Run nginx detached with a port mapping and fetch it with `curl`. Then run it without `-p` and
   confirm it is unreachable.
3. Run `docker run -it ubuntu bash`. Inside, run `ps aux` and `hostname`, then compare with the host.
   Note that your shell is PID 1.
4. Create a file inside that container, exit, remove it, start a new one, and confirm the file is
   gone.
5. Run a container with `-m 256m` and a program that allocates 512 MB. Observe the OOM kill and check
   the exit code (Day 079 — it is 137).
6. Use `docker exec` to open a shell in a running container and inspect its filesystem.
7. Create a user-defined network, run Postgres and a `psql` client container on it, and connect by
   container name. Then try `localhost` and confirm it fails.
8. Run `docker stop` on a container whose process ignores `SIGTERM` and time how long it takes.
9. Run `docker system df`, then `docker system prune`, and compare.
10. Pull an image by digest and confirm the tag and digest refer to the same content.

---

# 🚪 Exit questions

1. What is a container, in terms of the three kernel features?
2. Give three differences between a container and a VM, and one security consequence.
3. What is the relationship between an image and a container?
4. Why are ten containers from one image cheap on disk?
5. What happens to data written inside a container?
6. What does `-p 8080:80` mean, and which side is which?
7. Why does `localhost` not work between containers?
8. Why is fixing a problem with `docker exec` wrong?
9. Why is `:latest` unsafe, and what is the strongest alternative?
10. What signal does `docker stop` send, and how long does it wait?
11. Why does `docker logs` work without configuring anything?

## 🎙️ Articulation drill

Two minutes: **"What is Docker and why is it useful?"**

Define a container as a process with namespaces and cgroups rather than a small VM — that framing
alone distinguishes the answer. Then images as immutable layered artifacts, and the practical wins:
identical environments across dev and production, immutable deploys with trivial rollback, and
density. Close with the honest limitation: the kernel is shared, so isolation is weaker than a VM's.

---

**Previous:** [Day 129](Day-129.md) · **Next:** [Day 129B](Day-129B.md) — Dockerfiles and layers

> Tomorrow: writing your own image — layer caching, why instruction order decides your build time,
> and the JVM settings that a container changes.
