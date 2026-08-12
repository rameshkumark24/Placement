# Phase 09 — Observability

The question this phase answers: **when it breaks at 3am, do you find out from a dashboard or from
a user's tweet?**

**Gate to pass this phase:** Sentry live with source maps, alerts routed to something that wakes
you, spend alerts on every service.

---

## 1. Sentry — errors

- [ ] SDK installed for both client and server
- [ ] **Source maps uploaded** — a minified stack trace is useless
- [ ] Release tagging tied to the git SHA, so you know which deploy broke it
- [ ] Environment tagged (production / preview) so preview noise doesn't page you
- [ ] User context attached — **user ID only, never email or PII**
- [ ] **PII scrubbing on** — Sentry captures request bodies by default, which includes passwords
- [ ] `tracesSampleRate` set deliberately (0.1 is usually plenty; 1.0 burns quota fast)
- [ ] Source map upload in CI, not from your laptop

```ts
Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.VERCEL_ENV,
  release: process.env.VERCEL_GIT_COMMIT_SHA,
  tracesSampleRate: 0.1,
  sendDefaultPii: false,                    // ← keep this false
  beforeSend(event) {
    delete event.request?.cookies;
    if (event.request?.headers) delete event.request.headers['authorization'];
    return event;
  },
});
```

---

## 2. The 3am call — alerting that works

Most teams get this wrong in one of two directions: no alerts, or so many that everyone mutes them.

### What should wake you

- Error **rate spike** (not every individual error)
- A new issue in a critical path (checkout, signup, login)
- Payment webhook failures
- Uptime check failing on the real user-facing URL
- p95 latency past a threshold you chose deliberately
- **Spend crossing 80% of a cap**

### What should not

- Individual 404s
- Known third-party flakiness
- Validation errors from users typing wrong things
- Anything from preview deploys
- Bot traffic errors

### Rules

- [ ] Alerts routed somewhere that actually reaches you at 2am — **phone, not a muted Slack channel**
- [ ] Thresholds tuned **before** launch, not after
- [ ] Every alert has an owner and a first action
- [ ] Noisy alerts muted deliberately, with a note on why
- [ ] Alert on **symptoms users feel** (checkout failing), not internal metrics (CPU 80%)

> **Alert fatigue is the actual failure mode.** 200 notifications on day one, all muted by day
> three, and the real outage on day ten goes unseen. An alert channel you ignore is worse than no
> alerts, because it feels like coverage.

---

## 3. Uptime & health

- [ ] Uptime monitor on the **real user-facing URL**, not `/api/health`
- [ ] `/health` endpoint that actually checks the database, not just returns 200
- [ ] Checks from more than one region
- [ ] Alert after 2 consecutive failures, not 1 (avoids flapping)

```ts
// app/api/health/route.ts
export async function GET() {
  try {
    const { error } = await supabase.from('health_check').select('id').limit(1);
    if (error) throw error;
    return Response.json({ ok: true, ts: Date.now() });
  } catch {
    return Response.json({ ok: false }, { status: 503 });
  }
}
```

---

## 4. Logging

- [ ] Structured JSON logs with a **request / correlation ID** threaded through
- [ ] **PII redacted** — no emails, tokens, passwords, card details, addresses
- [ ] Log levels used properly; `console.log` debugging removed before merge
- [ ] Retention period set (cost control + compliance)
- [ ] Errors logged with enough context to reproduce: user ID, route, input shape — not the input itself

---

## 5. Product analytics

- [ ] Events from the [tracking plan](01-Scope-and-Planning.md#the-five-most-people-skip-and-regret) actually firing
- [ ] Funnel defined: landing → signup → activation → core action → payment
- [ ] Activation event identified — the moment a user gets value
- [ ] Dashboard for the Phase 01 success metrics
- [ ] Cookie consent respected before analytics fires (DPDP Act 2023 / GDPR)

**PostHog** for funnels, retention and session replay. **GA4** for traffic and acquisition. PostHog
answers "where do users drop off", which GA4 makes hard.

---

## 6. Spend monitoring — the alert people forget

A loop bug is a billing event before it's an error.

- [ ] **Billing alerts on every service**: Vercel, Supabase, Upstash, Sentry, Clerk, Pinecone, any LLM API
- [ ] Alerts at **50% and 80%** of your ceiling
- [ ] Vercel spend cap set
- [ ] Upstash budget alert — it bills per request, so a render loop bills you
- [ ] Sentry quota alert — an error loop burns a month's events in an hour
- [ ] Cost-per-request logged for the top 3 expensive endpoints
- [ ] **Bill checked daily for the first week** after launch

---

## 7. Dashboard

One page you can open during an incident:

| Panel | Source |
|---|---|
| Error rate + new issues | Sentry |
| p95 latency | Sentry / Vercel |
| Uptime | Uptime monitor |
| Signups + core action today | PostHog |
| Payment success rate | Stripe |
| DB connections + slow queries | Supabase |
| Spend vs cap | Each provider |

---

## Phase gate

- [ ] Sentry live with source maps and release tagging
- [ ] PII scrubbing verified — trigger a test error with a password field and check the event
- [ ] Alerts routed to a channel that wakes you, thresholds tuned
- [ ] `/health` checks the DB
- [ ] Analytics firing the planned events
- [ ] Spend alerts on every service
