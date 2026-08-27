# 🌐 Web Development — the vibe-coding library

**A dictionary, not a course.** Look things up when you need them. Works for any web project, not
one specific build.

---

## ⭐⭐ Start here — the two memory files

| File | What it is |
|---|---|
| ⭐⭐ **[AGENT-CONTEXT.md](AGENT-CONTEXT.md)** | **The memory.** Keep it in Notion. Paste it into a new chat before asking for any web work. Written *to the agent*, self-sufficient, and every other file here is depth behind a line in it. |
| ⭐⭐ **[CLAUDE-md-template.md](CLAUDE-md-template.md)** | **The per-project rules file.** Lives at the root of the repo, read automatically every session. |

> ⭐ **Use both.** Notion carries the rules *between* projects. `CLAUDE.md` carries the specifics *of*
> this one.

---

## The library

| File | Look here for |
|---|---|
| [00-Stack.md](00-Stack.md) | The services, the setup order, ⭐⭐ **spend caps**, env vars, and how to swap the stack |
| [01-Workflow.md](01-Workflow.md) | ⭐⭐ **Plan mode**, the session loop, prompting patterns, ⭐ **the Codex cross-check**, which AI tool for which job |
| [02-UI-System.md](02-UI-System.md) | ⭐⭐ **shadcn + [reactbits](https://reactbits.dev/)**, the AI-design tells to avoid, tokens, the four states |
| [03-Frontend.md](03-Frontend.md) | ⭐⭐ **The render loop that bills you**, state that drifts, keys, forms, data fetching |
| [04-Backend.md](04-Backend.md) | Schema, queries, API design, ⭐ **idempotency**, background jobs, files |
| [05-Security.md](05-Security.md) | ⭐⭐ **Is customer data actually safe** — the ID-swap test, RLS, secrets, payments, CSP |
| [06-Traffic-and-Scale.md](06-Traffic-and-Scale.md) | ⭐⭐ **Will it hold up** — the arithmetic, the database, caching, abuse, scaling triggers |
| [07-Performance.md](07-Performance.md) | Where the time goes, ⭐ **images**, fonts, the waterfall, bundle |
| [08-SEO-and-Meta.md](08-SEO-and-Meta.md) | ⭐ **Titles, meta descriptions, favicons**, Open Graph, broken links, clickable contact |
| [09-Testing.md](09-Testing.md) | The six-point skim, ⭐⭐ **the Codex final check**, tests worth having, real-device testing |
| ⭐⭐ [10-Ship-Checklist.md](10-Ship-Checklist.md) | **The pre-launch audit.** Nothing goes live until it passes. |
| [11-Post-Launch.md](11-Post-Launch.md) | Alerts, backups, incidents, the tech debt log, scaling triggers |
| ⭐⭐ [12-Legal-and-Compliance.md](12-Legal-and-Compliance.md) | **The four documents**, consent before tracking, account deletion, ⭐ **dependency and font licences**, email compliance, tax |
| [13-AI-Features.md](13-AI-Features.md) | ⭐ **If the product itself uses a model** — prompt injection, output as untrusted input, cost, hallucination |

**Reference:** [Component Libraries](Reference/Component-Libraries.md) ·
[API Notes](Reference/API-Notes.md)

---

## ⭐ By task — what do I need right now?

| I am… | Read |
|---|---|
| **Starting a project** | [00-Stack](00-Stack.md) → [CLAUDE-md-template](CLAUDE-md-template.md) → [01-Workflow](01-Workflow.md) |
| **Building the UI** | [02-UI-System](02-UI-System.md) |
| **Adding a feature** | [01-Workflow §1 plan mode](01-Workflow.md) → [03-Frontend](03-Frontend.md) / [04-Backend](04-Backend.md) |
| **Adding payments** | [04-Backend §4 idempotency](04-Backend.md) + [05-Security §6](05-Security.md) + [00-Stack §5](00-Stack.md) |
| **Adding auth** | [05-Security §1 §5](05-Security.md) |
| **Adding an AI feature** | ⭐⭐ [13-AI-Features.md](13-AI-Features.md) |
| **Sending email** | [04-Backend §9](04-Backend.md) — ⭐ SPF/DKIM/DMARC, or it lands in spam |
| **Asked “is this legal?”** | ⭐⭐ [12-Legal-and-Compliance.md](12-Legal-and-Compliance.md) |
| **Told "it's slow"** | [07-Performance](07-Performance.md) — ⭐ measure before you touch anything |
| **Asked "is it secure?"** | [05-Security](05-Security.md) — ⭐⭐ run the ID-swap test |
| **Asked "will it scale?"** | [06-Traffic-and-Scale](06-Traffic-and-Scale.md) — ⭐ do the arithmetic first |
| **About to launch** | ⭐⭐ [10-Ship-Checklist](10-Ship-Checklist.md) |
| **Just launched** | [11-Post-Launch](11-Post-Launch.md) |
| **Something broke** | [11-Post-Launch §4](11-Post-Launch.md) — ⭐ roll back first, debug second |

---

## The five rules

1. **Git before prompt.** Commit before every AI session — agents delete working code confidently.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist.** Client-side checks are decoration.
4. **Every API call needs a ceiling** — a loop guard, a retry cap, and a spend alert.
5. **Verify every package the agent adds.** AI invents names; attackers pre-register them.

---

## ⭐⭐ The three failures that cause the most damage

| Failure | Cause | Where |
|---|---|---|
| **Huge surprise bill** | A `useEffect` with no dependency array hammering an endpoint | [03-Frontend §1](03-Frontend.md) |
| **Total secret compromise** | `/.env` or `/.git/config` publicly readable | [05-Security §0](05-Security.md) |
| **Cross-user data leak** | No ownership filter in the query; RLS off | [05-Security §1](05-Security.md) |

---

## The 60-second check, any time

```
① ⭐⭐ ID-SWAP — can user A read user B's data?
② ⭐⭐ 320px — does it scroll sideways?
③ ⭐ Filter a list to zero — which empty state appears?
④ ⭐ Visit /nonsense — is there a real 404?
⑤ ⭐⭐ Paste the URL into WhatsApp — is there a preview card?
⑥ ⭐ Look at the browser tab — does it name the page?
⑦ ⭐ Click the email and phone — do they open apps?
⑧ ⭐⭐ Kill the network mid-action — error and retry, or a hang?
⑨ ⭐ Open the console — clean?
```

---

**Mobile:** [`04-App-Developer`](../04-App-Developer/) — same idea, different failures.
