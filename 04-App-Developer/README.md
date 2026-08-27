# 📱 App Development — the vibe-coding library

**A dictionary, not a course.** Look things up when you need them. Works for any mobile project.

---

## ⭐⭐ Start here — the two memory files

| File | What it is |
|---|---|
| ⭐⭐ **[AGENT-CONTEXT.md](AGENT-CONTEXT.md)** | **The memory.** Keep it in Notion. Paste it into a new chat before asking for any mobile work. Written *to the agent*, self-sufficient. |
| ⭐⭐ **[CLAUDE-md-template.md](CLAUDE-md-template.md)** | **The per-project rules file.** Lives at the repo root, read automatically every session. |

> ⭐ **Use both.** Notion carries the rules *between* projects. `CLAUDE.md` carries the specifics *of*
> this one.

---

## ⭐⭐ Five facts that drive everything here

```
① ⭐⭐ YOU CANNOT HOTFIX — a store review is 1–3 days
② ⭐⭐ OLD VERSIONS NEVER DIE — every API change is additive
③ ⭐⭐ THE NETWORK IS HOSTILE — offline is designed, not caught
④ ⭐⭐ THE OS KILLS YOU — without warning, at any moment
⑤ ⭐⭐ THE BUNDLE IS PUBLIC — there are no secrets in a mobile app
```

---

## The library

| File | Look here for |
|---|---|
| [00-Stack.md](00-Stack.md) | Services, setup order, spend caps, ⭐⭐ **IAP vs Stripe**, ⭐ **the OTA question** |
| [01-Workflow.md](01-Workflow.md) | ⭐⭐ **Plan mode + the native-code trigger**, prompting, ⭐ **the Codex cross-check**, why the simulator is not evidence |
| [02-UI-System.md](02-UI-System.md) | Native-feeling UI, ⭐⭐ **platform conventions**, tokens, the eight components, the five states |
| [03-App-Rules.md](03-App-Rules.md) | ⭐⭐ **The loops that cost battery**, listeners that leak, ⭐ **offline per screen**, the OS killing you, forms, notifications |
| [05-Security.md](05-Security.md) | ⭐⭐ **Is customer data safe on a device you don't control** — secure store, unzip your own build, the ID-swap test through a proxy |
| [06-Performance.md](06-Performance.md) | ⭐⭐ **60fps on a cheap Android** — lists, images, animation, cold start, memory, app size |
| [09-Testing.md](09-Testing.md) | The six-point skim, ⭐⭐ **the Codex final check**, ⭐ **the device matrix** |
| ⭐⭐ [10-Ship-Checklist.md](10-Ship-Checklist.md) | **The pre-submission audit.** Nothing submits until it passes. |
| [11-Release-and-After.md](11-Release-and-After.md) | ⭐⭐ **Staged rollout**, halting a bad release, feature flags as a kill switch, keeping old versions working |

**Reference:** [Component Libraries](Reference/Component-Libraries.md) ·
[**Distribution Options**](Reference/Distribution-Options.md) — with and without the stores

> **Do you actually need the stores?** For public iOS distribution, yes. Android has real
> alternatives, and a PWA skips both entirely.
> → [Distribution Options](Reference/Distribution-Options.md)

---

## ⭐ By task — what do I need right now?

| I am… | Read |
|---|---|
| **Starting a project** | [00-Stack](00-Stack.md) → ⭐⭐ decide IAP vs Stripe → [CLAUDE-md-template](CLAUDE-md-template.md) |
| **Building the UI** | [02-UI-System](02-UI-System.md) |
| **Adding a feature** | [01-Workflow §1](01-Workflow.md) → [03-App-Rules](03-App-Rules.md) |
| **Adding a library** | ⭐⭐ [01-Workflow §5](01-Workflow.md) — **does it add native code?** |
| **Adding payments** | ⭐⭐ [00-Stack §4](00-Stack.md) + [05-Security §5](05-Security.md) |
| **Adding auth** | [05-Security §1 §2](05-Security.md) — ⭐ tokens in secure store |
| **Adding a permission** | [05-Security §4](05-Security.md) — ⭐ at point of use, with a reason |
| **Told "it's slow / janky"** | [06-Performance](06-Performance.md) — ⭐ measure on a cheap Android |
| **Asked "is it secure?"** | [05-Security](05-Security.md) — ⭐⭐ unzip your own build |
| **Handling offline** | [03-App-Rules §4](03-App-Rules.md) |
| **About to submit** | ⭐⭐ [10-Ship-Checklist](10-Ship-Checklist.md) |
| **Just released** | [11-Release-and-After §5](11-Release-and-After.md) |
| **Something is broken in production** | ⭐⭐ [11-Release-and-After §3](11-Release-and-After.md) — **halt the rollout first** |

---

## The five rules

1. **Git before prompt.** Commit before every AI session.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist.**
4. **Every API call needs a ceiling** — loop guard, retry cap, spend alert. ⭐ On mobile it also
   costs the user battery and data.
5. **Verify every package** — ⭐⭐ and ask whether it adds native code.

---

## ⭐⭐ The three failures that cause the most damage

| Failure | Cause | Where |
|---|---|---|
| **A broken release you cannot recall** | No staged rollout, no force-update switch | [11-Release §2 §3](11-Release-and-After.md) |
| **Cross-user data leak** | No server-side ownership filter | [05-Security §2](05-Security.md) |
| **"Drains my battery" reviews** | A loop, a poll, or a leaked listener | [03-App-Rules §2 §3](03-App-Rules.md) |

---

## The 90-second check, any time

```
① ⭐⭐ ID-swap through a proxy — can A read B's data?
② ⭐⭐ Airplane mode — offline state, or a spinner?
③ ⭐ Every form with the keyboard up — is the button reachable?
④ ⭐⭐ Run it on a cheap Android — does it stutter?
⑤ ⭐ Filter a list to zero — which empty state?
⑥ ⭐⭐ Unzip the build and grep for secrets
⑦ ⭐ Largest system font — does text clip?
⑧ ⭐⭐ Force-kill mid-action and reopen — what state?
⑨ ⭐ Digital or physical goods — is the payment type right?
⑩ ⭐⭐ Deny every permission — does it still work?
```

---

**Web:** [`03-Web-Developer`](../03-Web-Developer/) — same idea, different failures.
