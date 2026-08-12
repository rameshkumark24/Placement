# 00 — Vibe Coding Core

Domain-independent rules for building anything with an AI coding agent. Every domain folder
(`01-Python-Developer` … `05-ML-Engineer`) assumes you have read these first and only documents
what is *different* for that stack.

This folder replaces the external "Vibe Coding" Notion page — the full content lives here now.

| File | Read it when |
|------|--------------|
| [Master-Build-Checklist.md](Master-Build-Checklist.md) | Starting any new app. 12 phases, top to bottom. |
| [API-Safety-and-Cost-Control.md](API-Safety-and-Cost-Control.md) | **Before wiring any API call.** Runaway loops, retry storms, spend caps. |
| [Security-Checklist.md](Security-Checklist.md) | Before every launch, and after any auth/data change. |
| [AI-Agent-Rules-Template.md](AI-Agent-Rules-Template.md) | First commit of a new repo. Drop in as `CLAUDE.md`. |
| [Prompting-Playbook.md](Prompting-Playbook.md) | Whenever the agent is drifting or producing slop. |
| [Reference/Saved-Links.md](Reference/Saved-Links.md) | Bookmarks. |

---

## The five rules that matter most

1. **Git before prompt.** Commit before every AI session. Agents delete working code confidently.
2. **Never merge code you can't explain line by line.** If you can't explain it, you can't debug it at 2am.
3. **Authorization is server-side or it doesn't exist.** Every client-side check is decoration.
4. **Every API call needs a ceiling** — a rate limit, a retry cap, and a spend alert. See
   [API-Safety-and-Cost-Control.md](API-Safety-and-Cost-Control.md).
5. **Verify every package the agent adds.** AI invents package names; attackers pre-register them.

---

## Why vibe-coded apps fail (in order of frequency)

| Failure | Where it comes from | Fix lives in |
|---|---|---|
| Data leak between users | No row-level security; client-side auth checks | [Security](Security-Checklist.md#authorization--the-idor-layer) |
| Surprise cloud bill | Infinite `useEffect` loop hammering an endpoint | [API Safety](API-Safety-and-Cost-Control.md#1-the-render-loop) |
| App dies at 100 users | No indexes, N+1 queries, no connection pooling | [Checklist §6](Master-Build-Checklist.md#6--performance--scalability) |
| Secret leaked in git | `.env` committed before `.gitignore` existed | [Security](Security-Checklist.md#data--secrets) |
| Cannot ship a fix | No tests, no staging, no rollback path | [Checklist §10](Master-Build-Checklist.md#10--pre-launch--deployment) |
| Malicious dependency | Hallucinated package name, squatted by an attacker | [Security](Security-Checklist.md#supply-chain) |
