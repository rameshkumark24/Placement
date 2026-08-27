# `CLAUDE.md` — the per-project rules file (mobile)

> **What this is.** [AGENT-CONTEXT.md](AGENT-CONTEXT.md) is how I work in general — it lives in Notion
> and I paste it into a chat. **This** lives in the repo, committed at the root, and is read
> automatically every session. ⭐⭐ **Use both.**

**Setup:** `/init` for a first draft, then replace with the template below and fill the brackets.

---

## The template — copy from here

```md
# Project: <name>

## What this is
<One sentence: who uses it and for what.>
<One sentence: what it must never do.>

## Platform constraints — these drive every decision
- ⭐ We CANNOT hotfix. A store review is 1–3 days. OTA (EAS Update) only
  fixes JavaScript — never native code, permissions, or app config.
- ⭐ Old app versions never die. Every API change is additive.
  NEVER a breaking change.
- The network is hostile. Offline is a designed state, not an error.
- The OS kills the app in the background without warning.
- ⭐ The bundle is public. There are NO secrets in this app.

## Stack — do not substitute without asking
- Expo (React Native) + TypeScript, Expo Router
- <TanStack Query with persistence> for server state
- expo-secure-store for tokens. <MMKV / AsyncStorage> for non-sensitive.
- <Supabase / our API> backend
- <Store IAP for digital goods | Stripe for physical goods>
- Sentry with native symbolication
- EAS Build + EAS Update
- Package manager: <pnpm>

## Commands
- Dev client: <pnpm start --dev-client>
- Typecheck / lint / test: <pnpm typecheck && pnpm lint && pnpm test>
- Build: <eas build --profile preview>

## ⭐⭐ Before adding ANY library
- Say whether it requires native code. If it does, STOP and tell me —
  that ends OTA for this change and costs a store review.
- Prefer Expo SDK modules over third-party native modules.

## Conventions
- Files kebab-case. Components PascalCase.
- Server state via TanStack Query. Never fetch-in-useEffect by hand.
- All API input validated with Zod at the boundary.
- Dates stored UTC, formatted at render.
- Money as integers in the smallest unit. Never floats.
- Every screen has loading, error, empty and offline states.
  Empty is two states: never-had-any, and filtered-to-zero.
- Lists use FlashList/FlatList. Never .map() over a long list.

## Design rules
- Respect platform conventions. Do not force iOS patterns onto Android.
- SafeAreaView / useSafeAreaInsets on every screen.
- Tap targets ≥ 44pt iOS / 48dp Android.
- Test every form with the keyboard open.
- Never ship placeholder text or fake people.

## Security rules — non-negotiable
- Tokens in expo-secure-store. NEVER AsyncStorage — it is plain text.
- ⭐ No secrets in the bundle. If a call needs a key, the app calls our
  API and our API holds it.
- Every DB query filters by the authenticated user, server-side.
- RLS on and forced.
- Permissions asked at point of use, with a reason. The screen must
  still work if denied.
- Uploads: validate content, cap size, strip EXIF.
- Never log PII — crash reports leave the device.
- Logout clears secure store, cache, query cache and files.
- Deep links are untrusted input. Validate them.

## Network & battery rules — non-negotiable
- ⭐ NO API call in render(). Ever.
- Every useEffect that fetches: correct primitive deps, cleanup returned.
- Every listener removed: AppState, NetInfo, keyboard, navigation.
- Retries max 3, exponential backoff with jitter, never 4xx except 429.
- ⭐ No background polling. Push notifications instead.
- Every request has a timeout.
- Offline writes queue and are idempotent.
- No large download on cellular without asking.

## Do NOT
- Do not add a dependency without naming it, why, and whether it
  needs native code.
- Do not run migrations, `git push`, `git reset --hard`, `git rebase`,
  or any store submission.
- Do not touch app.json, eas.json, .env, or the native folders
  without asking.
- Do not refactor files I did not ask you to change.
- Do not delete tests or weaken assertions to go green.
- ⭐ Do not claim something is tested if you only ran the simulator.
  Say "simulator only".
- Do not implement iOS only. Android is half the job.
- Do not agree with me if I ask for something that will leak data,
  drain battery, or get rejected. Say so first.

## Plan first when
- The change touches auth, payments, or user data.
- ⭐ ANYTHING adds native code, touches permissions, or changes
  app.json / eas.json.
- The change spans more than three files, or changes the schema.
- The API contract changes (old versions never die).
- The request is vague.

## Before you finish any task
1. Run <pnpm typecheck && pnpm lint && pnpm test>.
2. Tell me what changed and what to look at.
3. Say whether you ran it on a device, and which one.
4. Say what happens offline, and what happens if the OS kills the app.
5. Flag anything risky you saw but did not change.
```

---

## Keeping it honest

| ⭐ Rule | Why |
|---|---|
| **Update it when a convention changes** | ⭐⭐ A stale rule is followed confidently — worse than no rule |
| **Keep it under ~140 lines** | It is read every session |
| ⭐⭐ **The native-code question earns its place** | It is the one rule that protects your ability to ship a fix in five minutes |
| **Every rule should have cost you something once** | If you cannot remember why, delete it |

---

**Back:** [folder index](README.md) · **The memory:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md)
