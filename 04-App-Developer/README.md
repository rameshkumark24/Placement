# 📱 App Developer — Complete Domain

Everything needed to take a mobile app from idea to the stores and keep it alive. Self-contained:
nothing here depends on another folder.

**Work the phases in order.** Each one gates the next.

---

## Phases

| # | Phase | Gate to pass |
|---|-------|--------------|
| [00](00-Stack-and-Services.md) | **Stack & Services** | Store accounts registered, spend caps set |
| [01](01-Scope-and-Planning.md) | **Scope & Planning** | PRD, **versioned** API contract, auth matrix |
| [02](02-Design-System.md) | **Design System** | Tokens defined, four states per screen, platform rules chosen |
| [03](03-Architecture-and-Data.md) | **Architecture & Data** | ERD final, RLS tested, offline strategy decided per screen |
| [04](04-Build.md) | **Build** | `CLAUDE.md` committed, real device build by day two |
| [05](05-API-Safety.md) | **API Safety** | No API call in `build()`/render; every listener cleaned up |
| [06](06-Security.md) | **Security** | Tokens in SecureStore, no secrets in the bundle, IDOR test passed |
| [07](07-Performance.md) | **Performance** | 60fps on a **low-end** Android, cold start < 2s |
| [08](08-Testing-and-Review.md) | **Testing & Review** | `/code-review` + `/security-review` clean, real device matrix done |
| [09](09-Observability.md) | **Observability** | Sentry with native symbolication, crash-free rate visible |
| [10](10-Release.md) | **Release** | Store assets ready, OTA path verified, staged rollout planned |
| [11](11-Post-Launch.md) | **Post-Launch** | Crash-free > 99.5%, reviews monitored |

**Reference:** [Component Libraries](Reference/Component-Libraries.md) ·
[**Distribution Options**](Reference/Distribution-Options.md) — with and without the stores

> **Do you actually need the stores?** For public iOS distribution, yes — there is no legitimate
> way around Apple review. Android has real alternatives, and a PWA skips both entirely.
> Decide in [Phase 01](01-Scope-and-Planning.md): [Distribution Options](Reference/Distribution-Options.md)

---

## What makes mobile different from web

Read this before anything else — these five facts drive every decision in this folder.

1. **You cannot hotfix.** A web bug is fixed in 3 minutes. An App Store review takes 1–3 days.
   Set up OTA updates (EAS Update) **before** you need them.
2. **The network is hostile.** Users are on 3G, in lifts, on trains. Every request will fail
   sometimes. Offline behaviour is a feature, not an edge case.
3. **Nothing in the app bundle is secret.** Anyone can unzip an APK. Keys shipped in the app are
   public keys — treat them that way.
4. **Battery and data are the user's, not yours.** A polling loop that's merely wasteful on web
   gets your app uninstalled.
5. **The store is a gatekeeper.** Apple and Google reject apps for policy reasons that have nothing
   to do with whether the code works.

---

## The stack

| Layer | Service | Role |
|---|---|---|
| Framework | **React Native + Expo** | One codebase, most agent-friendly |
| Builds | **EAS Build** | Cloud builds — ship iOS without a Mac |
| OTA updates | **EAS Update** | Your kill switch and fast-fix path |
| Database | **Supabase** | Postgres, storage, realtime, RLS |
| Auth | **Clerk** | Sessions, MFA, biometrics |
| Payments | **Stripe** / IAP | Physical goods vs digital goods — the rules differ |
| Errors | **Sentry** | Native crashes + JS errors, the 3am call |
| Version control | **GitHub** | Repo, Actions CI, secret scanning |
| Edge / API | **Cloudflare** | WAF, CDN, R2 for assets |
| Redis | **Upstash** | Rate limiting — critical, since you can't patch clients fast |
| Vector DB | **Pinecone** | Semantic search / RAG, if the app has AI features |

Full setup and configuration: **[00-Stack-and-Services.md](00-Stack-and-Services.md)**

---

## The five rules

1. **Git before prompt.** Commit before every AI session.
2. **Never merge code you can't explain line by line.**
3. **Authorization is server-side or it doesn't exist** — and a mobile client is fully under the
   attacker's control.
4. **Every API call needs a ceiling** — a loop guard, a retry cap, and a spend alert.
5. **Verify every package the agent adds.**

---

## The three failures that cause the most damage

| Failure | Cause | Phase |
|---|---|---|
| Infinite request loop | An API call inside Flutter's `build()` or an RN render body | [05](05-API-Safety.md#1-the-render-loop) |
| Stolen sessions | Tokens in AsyncStorage instead of SecureStore | [06](06-Security.md#2-token-storage) |
| Rejected release | No in-app account deletion, or digital goods sold outside IAP | [10](10-Release.md#3-legal--privacy--where-most-rejections-happen) |

---

## Review gates

```
/code-review          # correctness bugs, simplification, efficiency
/security-review      # security review of pending changes on the branch
```

Details and Codex cross-checking: **[08-Testing-and-Review.md](08-Testing-and-Review.md)**
