# Phase 11 — Post-Launch

Launch is the start of the work, not the end of it. This phase loops back to
[Phase 01](01-Scope-and-Planning.md).

---

## First 48 hours

- [ ] **Error rate and latency watched hourly** — not daily
- [ ] Sentry issues triaged as they arrive; don't let a backlog form
- [ ] Payment success rate checked (Stripe dashboard)
- [ ] Signup → activation funnel checked — where are people dropping
- [ ] Support channel monitored and answered
- [ ] Rollback still ready

## First week

- [ ] **Bill checked daily.** This is when a loop bug or a missing rate limit shows up as money.
- [ ] Slow queries reviewed (Supabase → Query Performance)
- [ ] Uptime reviewed
- [ ] First real user feedback collected and written down
- [ ] Alert thresholds retuned against real traffic — the pre-launch guesses will be wrong

---

## Support & feedback

- [ ] Support channel live and monitored (email, form, or chat)
- [ ] Somewhere users can complain that you **actually read**
- [ ] Response time you can sustain, stated publicly
- [ ] Feedback logged somewhere reviewable, not just in your inbox
- [ ] Changelog / release notes published

---

## Ongoing rhythm

### Weekly
- [ ] Analytics reviewed — traffic, funnel, drop-off
- [ ] Search Console errors checked
- [ ] Sentry issues triaged
- [ ] Contact and signup forms tested manually
- [ ] Backups confirmed running

### Monthly
- [ ] `pnpm audit` + Dependabot PRs merged
- [ ] Dependencies updated (one cadence, not ad hoc)
- [ ] Broken links checked
- [ ] Lighthouse re-run — performance decays as features are added
- [ ] Spend reviewed against the cost model
- [ ] Tech debt log reviewed

### Quarterly
- [ ] **Full security review** — re-run the [Phase 06](06-Security.md) checklist end to end
- [ ] **Exposure check** — `/.env`, `/.git/config` still 404
- [ ] **Backup restore test** — actually restore, don't just confirm backups exist
- [ ] IDOR test re-run against new endpoints
- [ ] SEO audit
- [ ] UX review — watch a new user again
- [ ] Competitor check
- [ ] Access review: who still has access to Vercel, Supabase, Stripe, GitHub

---

## Scaling triggers — decide the numbers now

Write down, in advance, what number triggers what action:

| Signal | Threshold | Action |
|---|---|---|
| DB size | 80% of tier | Upgrade Supabase tier |
| DB connections | Sustained near limit | Check pooler config, then upgrade |
| p95 latency | > 1s | Profile, index, cache |
| Error rate | > 1% | Halt feature work, fix |
| Spend | 80% of ceiling | Investigate before raising the cap |
| Upstash requests | Rising without traffic rising | **Look for a loop** |
| Crash-free sessions | < 99.5% | Roll back |

Deciding these calmly now beats deciding them during an incident.

---

## Tech debt log

Start it on day one of post-launch, while you still remember the shortcuts.

For each entry: what you did, why, what breaks if it stays, and roughly what fixing it costs. A
shortcut you've written down is a decision; one you haven't is a landmine.

---

## After the first incident

- [ ] Post-incident note: what happened, why, how you found out, how long it took, what you changed
- [ ] **How you found out** matters most — if a user told you, your alerting has a gap
- [ ] Runbook updated
- [ ] Alert added if there wasn't one
- [ ] No blame; the process failed, not a person

---

## Iterating

- [ ] Feature requests weighed against the Phase 01 success metrics, not against who asked loudest
- [ ] The "later" list from Phase 01 revisited — some of it is now clearly unnecessary
- [ ] Every new feature goes through the same phases: plan → build → review → security → test → deploy
- [ ] `/code-review` and `/security-review` still run on every change — the discipline is what
      decays first once the app is live

> The most common post-launch failure is skipping the phases because "it's just a small change".
> Most production incidents come from small changes.
