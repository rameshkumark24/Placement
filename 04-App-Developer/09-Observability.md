# Phase 09 — Observability

On web you can watch a server log. On mobile, **the code runs on devices you'll never see**, in
network conditions you can't reproduce, on OS versions you don't own. Without instrumentation you
are blind.

**Gate to pass this phase:** Sentry capturing native crashes with symbolication, crash-free rate
visible, alerts routed somewhere that wakes you.

---

## 1. Sentry — errors and native crashes

A JS error and a native crash are different failures, and you need both.

- [ ] RN SDK capturing **JS errors and native crashes**
- [ ] **dSYMs (iOS) and ProGuard/R8 mappings (Android) uploaded** — without them a native crash is
      unreadable hex. This is the step people skip, and it makes the whole tool useless.
- [ ] JS source maps uploaded, matching the exact bundle
- [ ] Upload happens in **CI/EAS Build**, not from your laptop
- [ ] `release` and `dist` tagged to match your version and build number
- [ ] **OTA updates tagged too** — an EAS Update changes the JS bundle without changing the build
      number, so untagged updates produce unmappable stack traces
- [ ] **PII scrubbing on**; user ID only, never email
- [ ] `tracesSampleRate` set deliberately
- [ ] Breadcrumbs enabled — the last 20 actions before a crash are usually the whole story

```ts
Sentry.init({
  dsn: process.env.EXPO_PUBLIC_SENTRY_DSN,
  environment: __DEV__ ? 'development' : 'production',
  release: `${Application.applicationId}@${Application.nativeApplicationVersion}`,
  dist: Application.nativeBuildVersion,
  tracesSampleRate: 0.1,
  sendDefaultPii: false,                       // ← keep false
  enableNativeCrashHandling: true,
  beforeSend(event) {
    if (event.user) event.user = { id: event.user.id };   // strip email/IP
    return event;
  },
});
```

---

## 2. Crash-free rate — the headline metric

The number that tells you whether to ship, hold, or roll back.

| Metric | Target | Action if below |
|---|---|---|
| Crash-free **sessions** | > 99.5% | Halt rollout, investigate |
| Crash-free **users** | > 99% | Roll back |
| ANR rate (Android) | < 0.47% | Google demotes you above this |

- [ ] Watched **hourly for the first 48 hours** of a release
- [ ] Broken down by OS version and device model — a crash on one Android model is common
- [ ] Compared against the previous release, not against zero
- [ ] Play Console **Android Vitals** monitored — Google uses it for store ranking

---

## 3. The 3am call — alerting that works

### What should wake you

- Crash-free rate dropping below your threshold
- A **crash spike** after a release or OTA update
- Payment/IAP failures
- API error rate spike
- **Spend crossing 80% of a cap**
- Push delivery failures

### What should not

- Individual crashes on one obscure device
- Network errors from users with no connectivity (that's expected — track the rate, don't alert on each)
- Validation errors
- Anything from development builds

### Rules

- [ ] Alerts routed somewhere that reaches you at 2am — **phone, not a muted Slack channel**
- [ ] Thresholds tuned **before** release
- [ ] Every alert has an owner and a first action
- [ ] Alert on **rates and spikes**, not individual events — mobile generates far more noise than web
- [ ] Development and preview builds excluded from alerting

> **Alert fatigue is the real failure mode.** 200 notifications on day one, all muted by day three,
> and the crash that matters on day ten goes unseen.

---

## 4. What to instrument beyond crashes

Mobile-specific signals with no web equivalent:

- [ ] **App version distribution** — how many users are on old builds. This tells you whether a
      forced update is needed and how long old API versions must live.
- [ ] Cold start time, from real devices
- [ ] **Sync failure rate** and offline queue depth — a rising queue means writes aren't landing
- [ ] Push notification delivery and open rates
- [ ] Permission grant/deny rates per permission
- [ ] Screen load times for the critical path
- [ ] Network failure rate by request type
- [ ] OS and device model distribution — tells you what to test on
- [ ] Update adoption curve after each release

---

## 5. Backend observability

The API still needs everything a web backend needs:

- [ ] Structured logs with a request/correlation ID
- [ ] **PII redacted**
- [ ] `/health` that checks the DB
- [ ] p95 latency alerted
- [ ] Uptime monitoring
- [ ] Slow query review (Supabase → Query Performance)
- [ ] Rate-limit rejection rate tracked — **a spike means either an attack or a client-side loop bug**

---

## 6. Product analytics

- [ ] Events from the tracking plan firing
- [ ] Funnel: install → open → signup → activation → core action → payment
- [ ] **Activation event** identified — the moment a user gets value
- [ ] D1 / D7 / D30 retention tracked — the metric that matters most for apps
- [ ] Session length and frequency
- [ ] Uninstall tracking where the platform allows
- [ ] Consent respected before analytics fires

PostHog or Firebase Analytics. Attribute installs if you're spending on acquisition.

---

## 7. Spend monitoring

- [ ] Billing alerts on Supabase, Upstash, Sentry, Clerk, EAS, any LLM API
- [ ] Alerts at 50% and 80%
- [ ] **Upstash budget alert** — per-request billing, and a client loop keeps billing you until
      users update
- [ ] Sentry quota alert — a crash loop burns a month of events in an hour
- [ ] EAS build minutes watched
- [ ] **Bill checked daily for the first week** after release

---

## 8. Release dashboard

One page to open after every release:

| Panel | Source |
|---|---|
| Crash-free sessions (this release vs last) | Sentry |
| ANR rate | Play Console Vitals |
| New issues since release | Sentry |
| Rollout percentage | Play Console / App Store Connect |
| Version adoption | Analytics |
| API error rate + p95 | Sentry / backend |
| Rate-limit rejections | Upstash |
| Payment success rate | Stripe / RevenueCat |
| Store reviews (new, negative) | Store consoles |
| Spend vs cap | Each provider |

---

## Phase gate

- [ ] Sentry capturing JS **and** native crashes
- [ ] dSYMs and mappings uploaded from CI; a test crash symbolicates correctly
- [ ] OTA updates tagged with release/dist
- [ ] PII scrubbing verified on a real event
- [ ] Crash-free rate visible and thresholds set
- [ ] Alerts routed to a channel that wakes you
- [ ] Version distribution tracked
- [ ] Spend alerts on every service
