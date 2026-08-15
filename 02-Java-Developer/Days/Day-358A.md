# Day 358A ➕

**L-358A** · ➕ Design 15 — a distributed job scheduler and a metrics system

**Time:** 3 hrs · **Mode:** BUILD

> **⭐ Two infrastructure systems, one day, because both are things you will be asked to design and
> both have a single idea at their centre that people miss.**
>
> The scheduler's is that **"run at 9am" is a claim problem, not a timing problem.** The metrics
> system's is that **time-series data compresses about ten times better than general data — and that
> compression is the entire architecture.**

---

# Part 1 — ⭐ The job scheduler

| ⭐ Functional | ⭐ Non-functional |
|---|---|
| Schedule one-off and recurring jobs | ⭐⭐ **at-least-once execution, never zero** |
| ⭐ **Run exactly one instance of a job** | ⭐ fire within a few seconds of the due time |
| Retries with backoff | ⭐ **survive a scheduler crash** |
| ⭐ Job dependencies (a DAG) | scale: millions of scheduled jobs |
| Cancel, pause, backfill | ⭐ **a missed window must be a decision, not an accident** |

```
⭐ ESTIMATE
⭐   10M scheduled jobs · most daily ⇒ ⭐ ~120 due/s average
⭐   ⭐⭐ but cron is CLUSTERED: everything is scheduled at :00 ⇒ ⭐⭐ a huge spike on the hour
⭐   ⇒ ⭐ jitter, or the minute at the top of every hour is a thundering herd
```

## ⭐⭐ The core mechanism — a claim, not a timer

```
⭐ ✗ A timer per job                  ⭐⭐ 10M timers; all lost on restart
⭐ ✗ A cron process per node          ⭐⭐ every node runs every job
⭐
⭐ ✓ ⭐⭐ A DUE-TIME INDEX + COMPETING CLAIMERS:
⭐    jobs(next_run_at, status)  with an index on next_run_at
⭐    workers poll: "give me jobs due now" and CLAIM them atomically
```

```sql
-- ⭐ Exactly Day 316's conditional update, for the fourth time in this stage
UPDATE jobs SET status='RUNNING', owner=:worker, lease_until=now()+interval '5 minutes'
 WHERE id=:id AND (status='PENDING' OR (status='RUNNING' AND lease_until < now()));
-- ⭐ rowsAffected = 1 ⇒ you own it. 0 ⇒ someone else does. ⭐ No leader election needed.
```

| ⭐ Property | |
|---|---|
| ⭐⭐ **No leader, no consensus cluster** | ⭐⭐ **the database is the arbiter** (Day 343: remove the need before satisfying it) |
| ⭐ **Leases handle crashed workers** | ⭐ an expired lease makes the job claimable again — **automatically** |
| ⭐ **Long jobs renew the lease** | ⭐ a heartbeat; **if it stops, another worker takes over** |
| ⭐⭐ **At-least-once, so jobs must be idempotent** | ⭐⭐ **a worker can be paused past its lease and still be running** (Day 343) |

⚠️ **The zombie worker is the real hazard.** A worker that stalls past its lease is still executing
while another has claimed the job. **The job must be idempotent, or fenced** — pass the lease
generation into any external effect (Day 207's fencing token). **Naming this is what distinguishes
a design from a cron replacement.**

## ⭐ The missed-window decision

```
⭐ The scheduler was down 09:00–11:00. Three hourly jobs were due.
⭐ ⭐⭐ Run all three now? Run one? Skip? — ⭐⭐ THERE IS NO CORRECT DEFAULT.
```

| ⭐ Policy | ⭐ Right for |
|---|---|
| ⭐ **Catch up all** | ⭐ billing runs, report generation — **every window matters** |
| ⭐ **Run once, skip the rest** | ⭐ a cache refresh — **only the latest state matters** |
| ⭐ Skip entirely | ⭐ **a "good morning" notification at 11am is worse than none** |
| ⭐ **Fail and alert** | anything where a human should decide |

> **⭐ This must be a per-job configuration, and asking about it is the mark of someone who has
> operated a scheduler.** Every scheduler eventually has an outage, **and the recovery behaviour
> decides whether that outage becomes a second incident.**

**⭐ Dependencies turn it into a workflow engine:** a DAG where a job becomes eligible when its
parents have succeeded. **Say that this is where a scheduler stops being a scheduler**, and where
tools like Airflow or Temporal earn their place.

---

# Part 2 — ⭐⭐ The metrics system

| ⭐ Functional | ⭐ Non-functional |
|---|---|
| Ingest time-stamped numeric points with labels | ⭐⭐ **ingest must never block the sender** |
| ⭐ Query by time range and label filters | ⭐ query p99 < 1 s for a dashboard |
| ⭐ **Aggregate: rate, sum, percentile** | ⭐ **lossy under pressure is acceptable** — metrics are not money |
| Retention tiers | ⭐⭐ **cost per stored point is the binding constraint** |
| Alerting | |

```
⭐ ESTIMATE
⭐   10M active series · 1 point every 10 s ⇒ ⭐⭐ 1,000,000 points/s
⭐   ⭐ Naive: 16 B/point (8 timestamp + 8 value) ⇒ 16 MB/s ⇒ ⭐ 1.4 TB/day
⭐   ⭐⭐ COMPRESSED to ~1.4 B/point ⇒ ⭐⭐ ~120 GB/day — ⭐ a 10× difference
```

## ⭐⭐ The deep dive: time-series compression

**⭐ Time-series data has two properties nothing else has, and exploiting them is the whole design:**

```
⭐ 1. ⭐⭐ TIMESTAMPS ARE REGULAR
⭐    t: 1000, 1010, 1020, 1030 ...
⭐    delta:      10,   10,   10       ⭐ store deltas
⭐    ⭐ delta-of-delta: 0, 0, 0        ⭐⭐ store a single BIT for "same as last time"
⭐
⭐ 2. ⭐⭐ CONSECUTIVE VALUES ARE SIMILAR
⭐    XOR two adjacent float64s ⇒ ⭐ mostly zero bits ⇒ store only the meaningful window
```

| ⭐ Result | |
|---|---|
| ⭐⭐ **~1.4 bytes per point in practice** | ⭐⭐ **against 16 bytes naive — this is the Gorilla scheme** |
| ⭐ Compressed blocks held in memory | recent data answered without touching disk |
| ⭐ **Blocks are immutable once sealed** | ⭐ so they flush, replicate and cache trivially |
| ⭐ Out-of-order points are hard | ⭐ **the encoding assumes ordering — late points go to a separate path or are dropped** |

> **⭐ The line to say: general-purpose storage wastes an order of magnitude on this workload, and
> that order of magnitude is the reason time-series databases exist as a category.** It is not that
> PostgreSQL cannot store points — **it is that it stores them ten times more expensively, forever.**

## ⭐ The rest of the architecture

| Concern | ⭐ Design |
|---|---|
| ⭐ **Push or pull** | ⭐ pull gives the collector control and free liveness detection; **push is required for short-lived jobs** — do both, with a gateway for the latter |
| ⭐ Sharding | ⭐ **by series (hash of the label set)** so one series' points stay together |
| ⭐⭐ **Cardinality** | ⭐⭐ **the killer** (Day 338) — one unbounded label multiplies series without limit |
| ⭐ **Downsampling** | ⭐ raw for 7 days → 1-minute for 30 → 1-hour for 2 years — **the retention tier is the cost control** |
| Queries | ⭐ label index → series ids → decode blocks → aggregate |
| ⭐ **Lossy under pressure** | ⭐ **drop points rather than block the application** (Day 336) — a monitoring system must never take down what it monitors |

⚠️ **The last row is a design principle worth stating: metrics are the one system that must fail
open.** A blocking metrics client turns a monitoring outage into an application outage, **which is the
worst possible failure mode** — you lose the system *and* the ability to see why.

---

# Part 3 — ⭐ What both systems share

| ⭐ Pattern | ⭐ Scheduler | ⭐ Metrics |
|---|---|---|
| ⭐ **Conditional claim** | ⭐⭐ the lease update | — |
| ⭐ Leases and fencing | ⭐ zombie workers | — |
| ⭐ **Immutable, append-only** | job run history | ⭐⭐ sealed blocks |
| ⭐ **Derived state** | next_run_at recomputed | ⭐ downsampled rollups |
| ⭐ Failure policy is a *decision* | ⭐ catch up vs skip | ⭐ drop vs block |
| ⭐ **Idempotency required** | ⭐ at-least-once execution | at-least-once ingest |

> **⭐ Both are infrastructure that other systems depend on, which imposes an unusual requirement:
> they must fail in ways that do not amplify.** A scheduler that retries a broken job forever, or a
> metrics client that blocks, **turns one failure into a platform failure.**

```
⭐ SCHEDULER — MONITOR: ⭐ jobs overdue by > N seconds (the SLI) · lease takeovers (= crashes)
⭐              ⭐ retry rate · ⭐ jobs succeeded/min · DLQ depth
⭐ METRICS   — MONITOR: ⭐⭐ ingest drop rate · ⭐ active series count (cardinality!) · query p99
⭐              ⭐ bytes per point (⭐ compression working?) · block flush lag
⭐ CUT:       scheduler UI, backfills, complex DAGs · metrics tracing, logs, long retention
```

---

## ⭐ Traps

| Trap | Consequence |
|---|---|
| ⭐⭐ A timer per job | ⭐⭐ 10M timers, all lost on restart |
| ⭐ A cron process on every node | every job runs N times |
| ⭐ **Leader election where a conditional claim suffices** | ⭐ a consensus cluster you did not need (Day 343) |
| ⭐ No lease renewal for long jobs | the job is stolen mid-run |
| ⭐⭐ **Assuming exactly-once execution** | ⭐⭐ **a stalled worker plus a lease takeover means two runners** |
| ⭐ No missed-window policy | ⭐ **recovery becomes a second incident** |
| No jitter on the hour | a thundering herd at :00 |
| ⭐ **Storing metrics in a general database** | ⭐⭐ 10× the storage cost, forever |
| ⭐ Ignoring cardinality | ⭐ unbounded series growth (Day 338) |
| ⭐ **A blocking metrics client** | ⭐⭐ monitoring takes down the application |
| No downsampling or retention tiers | cost grows without limit |
| Out-of-order points into a delta-encoded block | the encoding assumes ordering |

## Interview questions

**Q: How do you make sure a scheduled job runs on exactly one machine?**
A due-time index plus competing claimers. Workers poll for jobs whose next run time has passed and
claim one with a conditional update that also sets a lease — rows affected decides the winner. No
leader election and no consensus cluster; the database is the arbiter. A crashed worker's lease
expires and the job becomes claimable again automatically, which is why the mechanism handles failure
without any extra machinery.

**Q: So it is exactly-once?**
No, at-least-once, and that distinction matters. A worker can stall past its lease while still
executing, so another worker takes the job and both run. The defences are making jobs idempotent, or
fencing — passing the lease generation into any external effect so a stale worker's writes are
rejected. A design that claims exactly-once here has not thought about the paused worker.

**Q: Your scheduler was down for two hours. What runs?**
Whatever the job says, because there is no correct default. A billing run should catch up every
missed window; a cache refresh should run once and skip the rest; a good-morning notification at
eleven is worse than none, so it should skip entirely; and anything a human should decide about
should fail and alert. Making that a per-job policy is what stops the recovery becoming a second
incident.

**Q: Why not store metrics in PostgreSQL?**
Because it costs about ten times more. Time-series data has two exploitable properties: timestamps
are regular, so delta-of-delta encoding reduces most of them to a single bit, and consecutive values
are similar, so XOR-ing adjacent floats leaves mostly zeros. That takes sixteen bytes a point down to
roughly one and a half. A general store cannot exploit either, and at a million points a second that
difference is one and a half terabytes a day versus a hundred and twenty gigabytes.

**Q: What must never happen in a metrics system?**
It must never block the application it monitors. A client that applies backpressure upstream turns a
monitoring outage into an application outage, and you lose both the system and the ability to see
why. So metrics drop points under pressure — it is the one system where lossy is unambiguously
correct, because a gap in a graph is vastly cheaper than a stalled request.

## Mini task

1. **Run the 45-minute framework on each**, timed, with **the claim mechanism** and **compression**
   as the two deep dives.
2. ⭐ Implement the **due-time index and conditional claim.** Run five workers against 1,000 due jobs
   and **assert each runs exactly once.**
3. ⭐⭐ **Kill a worker mid-job.** Confirm the lease expires and another claims it. **Then SIGSTOP a
   worker past its lease and demonstrate two runners** — the case you must design for.
4. ⭐ Implement **fencing** with the lease generation and confirm the stale worker's write is
   rejected.
5. ⭐ Implement all four **missed-window policies** and test each against a simulated two-hour outage.
6. Schedule 10,000 jobs at `:00` and **measure the spike.** Add jitter and re-measure.
7. ⭐⭐ Implement **delta-of-delta and XOR compression.** Encode a day of real-shaped data and
   **measure bytes per point** against naive storage.
8. ⭐ Feed **out-of-order points** into the encoder and observe what breaks. Design the separate path.
9. ⭐ Implement **downsampling tiers** and measure storage across raw, 1-minute and 1-hour rollups.
10. ⭐⭐ Make the metrics client **blocking**, then make the backend slow, and **watch it take down the
    application.** Make it lossy and repeat.

# 🚪 Exit questions

1. Why is a timer per job wrong, and what replaces it?
2. Write the claim statement and say what decides ownership.
3. Why is no leader election needed?
4. How are crashed workers handled, and how are long jobs kept?
5. Why is exactly-once unavailable, and what are the two defences?
6. Give the four missed-window policies with a use case for each.
7. When does a scheduler become a workflow engine?
8. Give the two exploitable properties of time-series data.
9. Explain delta-of-delta and XOR encoding, and the resulting ratio.
10. Why do time-series databases exist as a category?
11. Why must a metrics system be lossy, and what is the failure it prevents?
12. Give four patterns the two systems share.

## 🎙️ Articulation drill

**Two minutes each:** *"How do you run a job on exactly one machine?"* then *"Why not store metrics
in a normal database?"*

**⭐ The first answer's marker: you reach for a conditional claim with a lease before you reach for
leader election** — and then correct the premise of the question by saying it is at-least-once, not
exactly-once, because a paused worker plus a lease takeover means two runners.

**⭐ The second answer's marker is a number.** *"Sixteen bytes a point becomes about one and a half,
because timestamps are regular and adjacent values are similar — that ten-times difference is the
whole reason the category exists."* **Quantifying the reason beats naming the tool.**

---

**Previous:** [Day 358](Day-358.md) · **Next:** [Day 359](Day-359.md) — 🚪🚪 the Stage 9 exit gate:
**COMPLETE SDE**
