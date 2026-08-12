# Placement — Vibe Coding Playbooks & Interview Prep

Organised by the roles I'm applying for. Each domain folder is self-contained: a vibe-coding
cheatsheet for **building** in that stack, plus interview material for **getting hired** in it.

> **Start here:** [`00-Vibe-Coding-Core/`](00-Vibe-Coding-Core/) — the universal rules
> (security, API loop prevention, agent discipline). Every domain folder assumes you've read it and
> only documents what's different for that stack.

---

## Domains

| # | Domain | Build guide | Interview material |
|---|--------|-------------|--------------------|
| 00 | [Vibe Coding Core](00-Vibe-Coding-Core/) | Universal rules for every stack | — |
| 01 | [Python Developer](01-Python-Developer/) | [Cheatsheet](01-Python-Developer/Python-Vibe-Coding-Cheatsheet.md) | — |
| 02 | [Java Developer](02-Java-Developer/) | [Cheatsheet](02-Java-Developer/Java-Vibe-Coding-Cheatsheet.md) | [Core/](02-Java-Developer/Core/) |
| 03 | [Web Developer](03-Web-Developer/) | [Cheatsheet](03-Web-Developer/Web-Development-Cheatsheet.md) | — |
| 04 | [App Developer](04-App-Developer/) | [Cheatsheet](04-App-Developer/App-Development-Cheatsheet.md) | — |
| 05 | [ML Engineer](05-ML-Engineer/) | [Cheatsheet](05-ML-Engineer/ML-Vibe-Coding-Cheatsheet.md) | [Fundamentals/](05-ML-Engineer/Fundamentals/), [GenAI/](05-ML-Engineer/GenAI/), [Data-Science/](05-ML-Engineer/Data-Science/) |
| 06 | [Common](06-Common/) | Cross-domain: SQL, Cloud & DevOps, Golang | [HR-Interview/](06-Common/HR-Interview/) |

---

## The core files, wherever you're working

| File | When |
|---|---|
| [Master-Build-Checklist.md](00-Vibe-Coding-Core/Master-Build-Checklist.md) | Starting any new app — 12 phases, top to bottom |
| [API-Safety-and-Cost-Control.md](00-Vibe-Coding-Core/API-Safety-and-Cost-Control.md) | **Before wiring any API call** — loops, retries, spend caps |
| [Security-Checklist.md](00-Vibe-Coding-Core/Security-Checklist.md) | Before every launch, and after any auth change |
| [AI-Agent-Rules-Template.md](00-Vibe-Coding-Core/AI-Agent-Rules-Template.md) | First commit of a new repo — drop in as `CLAUDE.md` |
| [Prompting-Playbook.md](00-Vibe-Coding-Core/Prompting-Playbook.md) | When the agent is drifting or producing slop |

---

## The five rules

1. **Git before prompt.** Commit before every AI session.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist.**
4. **Every API call needs a ceiling** — rate limit, retry cap, spend alert.
5. **Verify every package the agent adds.** AI invents names; attackers squat them.

---

## Repo map

```
00-Vibe-Coding-Core/     Master checklist · API safety · Security · Agent rules · Prompting
01-Python-Developer/     FastAPI/Django backend cheatsheet
02-Java-Developer/       Spring Boot cheatsheet · Core Java interview notes · Spring Boot reference
03-Web-Developer/        Web cheatsheet · UI libraries · Deploy/Analytics/SEO
04-App-Developer/        Mobile cheatsheet · UI libraries · Store release checklist
05-ML-Engineer/          ML cheatsheet · Fundamentals · Deep Learning · GenAI · MLOps · Data Science
06-Common/               SQL · Golang · Cloud & DevOps (AWS, CI/CD, Docker) · HR Interview
```
