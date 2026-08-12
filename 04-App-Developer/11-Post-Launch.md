# Phase 11 — Post-Launch

Release is the start of the work. This phase loops back to
[Phase 01](01-Scope-and-Planning.md).

---

## First 48 hours

- [ ] **Crash-free session rate watched hourly** — halt the rollout below 99%
- [ ] ANR rate watched (Android Vitals)
- [ ] New Sentry issues triaged as they arrive
- [ ] API error rate and p95 latency watched
- [ ] Payment / IAP success rate checked
- [ ] Store reviews read and replied to
- [ ] Rollout percentage increased **only** if the numbers hold
- [ ] Rollback or OTA fix ready

## First week

- [ ] **Bill checked daily.** This is when a loop bug or missing rate limit shows up as money.
- [ ] **Upstash rejection rate checked** — a spike means an attack or a client-side loop
- [ ] Slow queries reviewed
- [ ] Funnel checked: install → open → signup → activation
- [ ] D1 retention measured — the earliest signal of whether the app works
- [ ] Alert thresholds retuned against real traffic
- [ ] First real feedback collected and written down

---

## Support & reviews

- [ ] Support channel live and monitored
- [ ] **Store reviews replied to** — both stores let you respond, and it measurably lifts ratings
- [ ] Negative reviews triaged: is it a bug, a missing feature, or a misunderstanding?
- [ ] Review prompt triggered **after a success moment**, never on launch
      (`expo-store-review` / the native APIs — do not build your own dialog)
- [ ] Feedback logged somewhere reviewable
- [ ] Changelog / release notes written for each update

---

## Ongoing rhythm

### Weekly
- [ ] Crash-free rate and new issues
- [ ] Store reviews and ratings
- [ ] Analytics — retention, funnel, activation
- [ ] **Version distribution** — how many users are on old builds
- [ ] Sync failure rate and offline queue depth
- [ ] Backups confirmed running

### Monthly
- [ ] `pnpm audit` + Dependabot merged
- [ ] Dependencies updated on one cadence
- [ ] `npx expo-doctor` for SDK/native drift
- [ ] Performance re-measured on a **low-end device** — it decays as features are added
- [ ] Spend reviewed against the cost model
- [ ] Tech debt log reviewed
- [ ] **Store policy changes checked** — Google raises the target API level annually, Apple changes
      guidelines regularly, and both will eventually block updates if you're behind

### Quarterly
- [ ] **Full security review** — re-run [Phase 06](06-Security.md) end to end
- [ ] **Exposure check** — `/.env`, `/.git/config` still 404 on the API domain
- [ ] **Backup restore test** — actually restore, don't just confirm backups exist
- [ ] IDOR test re-run against new endpoints
- [ ] Expo SDK upgrade planned (falling more than two versions behind gets painful)
- [ ] Old API versions reviewed for retirement
- [ ] Access review: who still has access to the store consoles, Supabase, Stripe, GitHub
- [ ] Full device matrix re-tested

---

## API version retirement

You cannot break an endpoint an old client still calls. Retirement is a process:

1. Ship a new app version using `/v2`
2. Watch version distribution until `/v1` usage drops below your threshold
3. **Trigger the forced update** for remaining old clients
4. Wait for that to propagate
5. Only then retire `/v1`

- [ ] Deprecation policy written and followed
- [ ] Old-version usage tracked per endpoint
- [ ] Forced-update tested before you rely on it

---

## Scaling triggers — decide the numbers now

| Signal | Threshold | Action |
|---|---|---|
| Crash-free sessions | < 99.5% | Halt rollout, investigate |
| Crash-free sessions | < 99% | Roll back or OTA fix |
| ANR rate | > 0.47% | Fix immediately — Google demotes you |
| DB size | 80% of tier | Upgrade Supabase |
| p95 API latency | > 1s | Profile, index, cache |
| Spend | 80% of ceiling | Investigate before raising the cap |
| **Upstash requests rising without user growth** | any | **Look for a client-side loop** |
| Users on the oldest supported version | < 5% | Retire that API version |

Deciding these calmly now beats deciding them during an incident.

---

## After the first incident

- [ ] Post-incident note: what happened, why, **how you found out**, how long it took, what changed
- [ ] If a user told you first, your alerting has a gap
- [ ] Runbook updated
- [ ] Alert added if there wasn't one
- [ ] If the fix required a store release, ask why it wasn't OTA-able — that answer usually
      improves the architecture
- [ ] No blame; the process failed, not a person

---

## Tech debt log

Start it on day one, while you still remember the shortcuts. For each: what you did, why, what
breaks if it stays, roughly what fixing it costs.

A shortcut you've written down is a decision. One you haven't is a landmine.

---

## Iterating

- [ ] Feature requests weighed against the Phase 01 success metrics, not against who asked loudest
- [ ] The "later" list revisited — some of it is now clearly unnecessary
- [ ] Every new feature goes through the same phases
- [ ] `/code-review` and `/security-review` still run on every change — **the discipline decays
      first once the app is live**, and on mobile a mistake takes days to fix

> The most common post-launch failure is skipping the phases because "it's just a small change".
> Most production incidents come from small changes.
