# 📡 Post-Launch — the part nobody plans

> ⭐⭐ **Launch is not the end of the work, it is the start of a different job.** The failures after
> launch are not bugs in features — they are things you never set up: no alert, no backup you have
> restored, no way for a user to tell you something is broken.

---

# 1 · The first 48 hours

```
□ ⭐ WATCH SENTRY. A new error you have never seen is normal on day one.
   ⭐⭐ TRIAGE IT: is it one user's browser extension, or everyone?
□ ⭐ WATCH THE BILL. Every service, once a day, for a week.
   ⭐⭐ THIS IS WHEN A LOOP SHOWS UP.
□ Watch the database — CPU, connections, slow queries
□ ⭐ COMPLETE THE MAIN WORKFLOW YOURSELF, from a fresh account, on a
   phone, on mobile data
□ ⭐⭐ CHECK THE FUNNEL. Started vs completed. If people start and do
   not finish, THAT is your bug, and no error was logged.
□ Reply to every piece of feedback, even the rude ones
□ ⭐ DO NOT SHIP FEATURES. Fix what launch revealed.
```

---

# 2 · ⭐⭐ Alerts that reach you

```
⭐⭐ AN ALERT YOU DO NOT SEE IS NOT AN ALERT. AN ALERT THAT FIRES
   CONSTANTLY IS ALSO NOT AN ALERT — you will mute it and then miss
   the real one.

 ⭐ ALERT ON THESE — TO YOUR PHONE:
  □ the site is down (uptime check from outside your host)
  □ error rate above your normal
  □ ⭐⭐ SPEND ABOVE A THRESHOLD — the one people forget
  □ payment failures spiking
  □ the database near its connection or CPU limit
  □ queue depth growing / jobs failing repeatedly
  □ ⭐ CERTIFICATE OR DOMAIN EXPIRY — a silent, total, avoidable outage

 ❌ DO NOT ALERT ON: individual 4xx · every caught exception ·
    deploys · anything you will not act on at 3am
```

```
⭐ THE TEST: TRIGGER EACH ALERT ON PURPOSE, ONCE. If it does not reach
  your phone, it does not exist. ⭐⭐ MOST ALERTING IS BROKEN AND NOBODY
  KNOWS UNTIL THE INCIDENT.
```

---

# 3 · Backups

```
□ Automated, daily, retained for at least 30 days
□ ⭐⭐ RESTORE ONE. ONCE. TO A SCRATCH DATABASE.
   ⭐ AN UNTESTED BACKUP IS NOT A BACKUP — it is a belief. Half of them
     do not restore, and you find out on the day it matters.
□ ⭐ KNOW YOUR RECOVERY TIME. "How long from 'the data is gone' to
   'the site works'?" If you do not know, it is longer than you think.
□ Uploaded files are backed up too — ⭐ people forget object storage
□ Backups are not in the same account that could be compromised
```

---

# 4 · When something breaks

```
⭐⭐ THE ORDER. DO NOT DEBUG FIRST.

 ① ⭐ STOP THE BLEEDING — roll back. Do not debug in production while
    users are affected. ⭐⭐ ROLLBACK IS SECONDS AND YOU HAVE TESTED IT.
 ② ⭐ TELL PEOPLE. A status note beats silence. Silence is what makes
    users leave.
 ③ ⭐⭐ CAPTURE EVIDENCE BEFORE IT ROTATES — logs, screenshots, the
    request id, the time window.
 ④ THEN find the cause, with the pressure off.
 ⑤ ⭐ FIX FORWARD, with a test that would have caught it.
 ⑥ ⭐⭐ WRITE IT DOWN. Five lines: what happened · what it affected ·
    what caused it · what you changed · what would have caught it
    earlier.
```

> ⭐ **Blame the missing guardrail, not the mistake.** "I forgot" is not a cause. "There was no
> constraint that would have refused it" is a cause, and it has a fix.

---

# 5 · Ongoing rhythm

| ⭐ When | Do |
|---|---|
| **Daily (first week)** | Sentry, the bill, the funnel |
| **Weekly** | Dependency PRs · error trends · slow queries · ⭐ the bill |
| **Monthly** | ⭐⭐ Restore a backup · rotate anything overdue · review spend caps · check Search Console |
| **Quarterly** | ⭐ Re-run the [security audit](05-Security.md) — especially the ID-swap test · re-run the [ship checklist](10-Ship-Checklist.md) · load test again |

---

# 6 · ⭐⭐ Scaling triggers — decide the numbers now

Write these down while calm, so you act on a number instead of a feeling.

| When | Do |
|---|---|
| p95 > 500ms | Profile. ⭐ Find the query. |
| DB CPU > 70% sustained | Index, cache, or size up |
| Connections > 70% of limit | ⭐⭐ Fix or add the pooler |
| Error rate > 1% | ⭐ Stop shipping features |
| Any table > 1M rows | Review its indexes and queries |
| Bill up > 30% month on month | ⭐ Find out what, before it compounds |
| One endpoint > 40% of load | Cache it or rethink it |

---

# 7 · The tech debt log

```
⭐⭐ EVERY TIME YOU SHIP SOMETHING YOU KNOW IS NOT RIGHT, WRITE ONE LINE:

   <what> · <why it is wrong> · <what it will cost later> · REVISIT WHEN

 ⭐ Example:
   "No pagination on /admin/users · loads every row · breaks around
    10k users · REVISIT WHEN we pass 2,000 users"

⇒ ⭐⭐ THE "REVISIT WHEN" IS THE WHOLE VALUE. It turns a vague worry
  into a trigger, and it turns a shortcut into a decision.
  ⭐ It is also the honest answer to "what would you do differently?"
```

---

# 8 · Feedback

```
□ ⭐ A WAY TO REPORT A PROBLEM that is one click from where it happened
□ ⭐⭐ THE REPORT CARRIES THE REQUEST ID AND THE PAGE — otherwise you
   get "it's broken" and can do nothing with it
□ Every contact channel you publish is one you must actually read
□ ⭐ WATCH THE FUNNEL, NOT JUST ERRORS. People who give up silently do
   not file a bug — and they are the majority.
□ Ask five real users to complete the main task while you watch.
   ⭐⭐ TWENTY MINUTES OF THIS BEATS A MONTH OF GUESSING.
```

---

# 9 · Keeping it alive

```
□ ⭐ DEPENDENCY UPDATES WEEKLY, as reviewable PRs. ⭐⭐ Six months of
   skipping them turns a routine update into a migration project.
□ ⭐ DO NOT AUTO-ADOPT BRAND-NEW VERSIONS — a compromised release is
   usually pulled within hours
□ Domain and certificate auto-renew — ⭐ and an expiry alert anyway
□ ⭐⭐ RE-RUN THE ID-SWAP TEST after any auth or data change
□ Delete features nobody uses. ⭐ Every feature is maintenance forever.
□ ⭐ KEEP CLAUDE.md CURRENT. A stale rule is followed confidently,
   which is worse than no rule.
```

---

**Back:** [folder index](README.md) · **Security:** [05-Security.md](05-Security.md) ·
**Scale:** [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md)
