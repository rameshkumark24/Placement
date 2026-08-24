# Placement — Domain Playbooks & Interview Prep

Organised by the roles I'm applying for. **[Web Developer](03-Web-Developer/)** and
**[App Developer](04-App-Developer/)** are complete, self-contained, phase-by-phase build domains —
open one and everything you need for that kind of project is inside it.

---

## Domains

| # | Domain | What's inside |
|---|--------|---------------|
| 01 | [Python Developer](01-Python-Developer/) | FastAPI backend cheatsheet |
| 02 | [Java Developer](02-Java-Developer/) | Spring Boot cheatsheet + core Java interview prep |
| 03 | [**Web Developer**](03-Web-Developer/) | **Complete 12-phase domain** — idea to live and maintained |
| 04 | [**App Developer**](04-App-Developer/) | **Complete 12-phase domain** — idea to store and maintained |
| 05 | [ML Engineer](05-ML-Engineer/) | Classical ML, deep learning, data science, MLOps — ⭐⭐ **GenAI/LLM work is [Python Days 414–461](01-Python-Developer/Days/)** |
| 06 | [Common](06-Common/) | SQL · Golang · Cloud & DevOps · HR interview |

---

## The two build domains

Both follow the same twelve phases. Each phase gates the next.

| # | Phase | [Web](03-Web-Developer/) | [App](04-App-Developer/) |
|---|-------|---|---|
| 00 | Stack & Services | [→](03-Web-Developer/00-Stack-and-Services.md) | [→](04-App-Developer/00-Stack-and-Services.md) |
| 01 | Scope & Planning | [→](03-Web-Developer/01-Scope-and-Planning.md) | [→](04-App-Developer/01-Scope-and-Planning.md) |
| 02 | Design System | [→](03-Web-Developer/02-Design-System.md) | [→](04-App-Developer/02-Design-System.md) |
| 03 | Architecture & Data | [→](03-Web-Developer/03-Architecture-and-Data.md) | [→](04-App-Developer/03-Architecture-and-Data.md) |
| 04 | Build | [→](03-Web-Developer/04-Build.md) | [→](04-App-Developer/04-Build.md) |
| 05 | **API Safety** | [→](03-Web-Developer/05-API-Safety.md) | [→](04-App-Developer/05-API-Safety.md) |
| 06 | **Security** | [→](03-Web-Developer/06-Security.md) | [→](04-App-Developer/06-Security.md) |
| 07 | Performance | [→](03-Web-Developer/07-Performance.md) | [→](04-App-Developer/07-Performance.md) |
| 08 | Testing & Review | [→](03-Web-Developer/08-Testing-and-Review.md) | [→](04-App-Developer/08-Testing-and-Review.md) |
| 09 | Observability | [→](03-Web-Developer/09-Observability.md) | [→](04-App-Developer/09-Observability.md) |
| 10 | Deploy / Release | [→](03-Web-Developer/10-Deploy-and-Launch.md) | [→](04-App-Developer/10-Release.md) |
| 11 | Post-Launch | [→](03-Web-Developer/11-Post-Launch.md) | [→](04-App-Developer/11-Post-Launch.md) |

---

## The stack

| Layer | Service | Role |
|---|---|---|
| Hosting | Vercel | Deploys, preview builds |
| Mobile builds | Expo EAS | Cloud builds + OTA updates |
| Database | Supabase | Postgres, storage, RLS |
| Auth | Clerk | Sessions, MFA, roles |
| Payments | Stripe / IAP | Physical vs digital goods — different rules |
| Errors | Sentry | The 3am call |
| Version control | GitHub | CI, secret scanning, Dependabot |
| Edge / DNS | Cloudflare | WAF, CDN, bot protection, R2 |
| Redis | Upstash | Rate limiting, caching, queues |
| Vector DB | Pinecone | RAG / semantic search |

---

## The five rules

1. **Git before prompt.** Commit before every AI session.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist.**
4. **Every API call needs a ceiling** — loop guard, retry cap, spend alert.
5. **Verify every package the agent adds.** AI invents names; attackers squat them.

---

## Review gates

Run on every agent-written diff:

```
/code-review          # correctness bugs + simplification/efficiency
/security-review      # security review of pending changes on the branch
/code-review ultra    # deep multi-agent cloud review, before a launch
```

---

## The three failures that cause the most damage

| Failure | Cause | Where |
|---|---|---|
| Huge surprise bill | An API call in a render body / `useEffect` with no dep array | Phase 05 |
| Total secret compromise | `/.env` or `/.git/config` publicly readable | Phase 06 |
| Cross-user data leak | No ownership filter in the query; RLS off | Phase 06 |

Check the second one right now on anything you've deployed:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://yourdomain.com/.env
curl -s -o /dev/null -w "%{http_code}\n" https://yourdomain.com/.git/config
```

Both must be `404`.
