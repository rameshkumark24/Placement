# 🔎 Verification Tools — checking your own work from outside

> ⭐⭐ **You cannot review what you did not write, and you are vibe coding.** These are the external
> checks that grade your site without your opinion involved. **Most are free, most take under two
> minutes, and none of them care what you meant to build.**

**Run the whole table before launch** ([10-Ship-Checklist.md](../10-Ship-Checklist.md)), and the
security ones again after any infrastructure change.

---

# The table

| Tool | Checks | ⭐ Target | ⭐⭐ What it does *not* catch |
|---|---|---|---|
| **[SSL Labs](https://www.ssllabs.com/ssltest/)** | TLS config, certificate chain, protocol versions, cipher suites | ⭐ **A or A+** | ⭐⭐ Nothing about your app. A perfect TLS grade on an app leaking data is normal. |
| **[Security Headers](https://securityheaders.com/)** | The response headers you set — CSP, HSTS, X-Content-Type-Options, Referrer-Policy, Permissions-Policy | ⭐ **A** | ⭐⭐ Whether your CSP is *real*. `unsafe-inline` can still score well while disabling the protection. |
| **[DesignMeter](https://designmeter.ai/)** | AI design analysis — visual hierarchy, usability, UX, with specific suggestions | ⭐ A second opinion | ⭐⭐ It is a signal, not a verdict. It cannot tell you whether the product makes sense. |
| **Lighthouse** (in Chrome DevTools) | Performance, accessibility, best practices, SEO | ⭐ **≥ 90 mobile** | ⭐ Real-user performance. Run it on the **deployed** URL, mobile profile. |
| **[mail-tester.com](https://www.mail-tester.com/)** | Email deliverability — SPF, DKIM, DMARC, spam score | ⭐ **9/10+** | ⭐⭐ Whether Gmail *specifically* filters you. Send yourself a real one too. |
| **`npx linkinator <url> --recurse`** | Broken internal and external links | ⭐ **0 broken** | Links behind auth |
| **axe DevTools** / WAVE | Contrast, missing labels, ARIA misuse | ⭐ 0 serious | ⭐⭐ ~70% of real issues. **It cannot judge focus order or whether your tab flow makes sense.** |
| **`npx license-checker --summary`** | Dependency licences | ⭐ **No AGPL/GPL** | Asset licences — fonts, icons, images. Those are manual. |
| **`npm audit --omit=dev`** | Known vulnerable dependencies | ⭐ 0 high | ⭐⭐ A package that is malicious *today* and undiscovered |
| **Paste the URL into WhatsApp / Slack** | Open Graph card | ⭐ A real preview | — ⭐ Better than any OG validator, because it is the actual renderer |

---

# ⭐⭐ The checks no tool performs

```
⭐⭐ EVERY TOOL ABOVE CHECKS CONFIGURATION. NONE OF THEM CHECK YOUR
   LOGIC — AND YOUR LOGIC IS WHERE THE EXPENSIVE BUGS LIVE.

 ① ⭐⭐ THE ID-SWAP TEST — log in as user A, request user B's id.
    ⭐ NO SCANNER FINDS THIS. It is the #1 real data leak, it takes
      60 seconds, and it needs a human with two accounts.
 ② ⭐ 320px WIDTH — does the page scroll sideways?
 ③ ⭐ FILTER A LIST TO ZERO — which empty state appears?
 ④ ⭐⭐ KILL THE NETWORK MID-ACTION — an error and a retry, or a hang?
 ⑤ ⭐ TAB THROUGH THE PAGE WITH NO MOUSE
 ⑥ ⭐⭐ curl /.env AND /.git/config — both must 404
 ⑦ ⭐ grep THE BUILD FOR SECRETS
 ⑧ ⭐⭐ THE CODEX CROSS-CHECK on money, auth and customer data
    (→ [01-Workflow.md §4](../01-Workflow.md))
```

---

# ⭐ Reading a grade honestly

```
⭐⭐ A GRADE IS A FLOOR, NOT A CERTIFICATE.

  "A+ on SSL Labs" ⇒ ⭐ your TLS is configured well.
                   ⇒ ⭐⭐ IT SAYS NOTHING ABOUT WHETHER STRANGERS CAN
                     READ YOUR CUSTOMERS' DATA.
  "A on Security Headers" ⇒ ⭐ the headers exist.
                   ⇒ ⭐⭐ A CSP WITH 'unsafe-inline' STILL SCORES —
                     and it has disabled the protection you wanted.
  "90+ on Lighthouse" ⇒ ⭐ good on ONE device on ONE connection.
                   ⇒ ⭐⭐ FIELD DATA FROM REAL USERS CAN DISAGREE,
                     and the field data is the truth.
  "Design score 8/10" ⇒ ⭐ a useful second opinion on hierarchy and
                     contrast. ⭐⭐ IT CANNOT TELL YOU THE PRODUCT IS
                     CONFUSING.

⇒ ⭐ USE THEM TO CATCH WHAT YOU FORGOT, NEVER TO CONCLUDE YOU ARE DONE.
```

---

# The two-minute pass

```
① ssllabs.com/ssltest        ⇒ A or better?
② securityheaders.com        ⇒ A? And is CSP real, or unsafe-inline?
③ Lighthouse, mobile         ⇒ 90+?
④ npx linkinator             ⇒ 0 broken?
⑤ paste the URL in WhatsApp  ⇒ preview card?
⑥ ⭐⭐ THE ID-SWAP TEST        ⇒ the one no tool does
```

---

**Back:** [folder index](../README.md) · **Security:** [05-Security.md](../05-Security.md) ·
**Ship:** [10-Ship-Checklist.md](../10-Ship-Checklist.md)
