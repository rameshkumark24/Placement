# 🔁 Workflow — driving the agent on mobile

> ⭐⭐ **Everything in the [web version](../03-Web-Developer/01-Workflow.md) applies. This file is what
> mobile adds** — and mobile adds real constraints: you cannot hotfix, the simulator lies, and one
> library choice can end your ability to ship a fix in five minutes.

---

# 1 · ⭐⭐ Plan mode — and the mobile-only triggers

**Plan first for all the web reasons, plus these:**

| Mobile trigger | Why |
|---|---|
| ⭐⭐ **Anything that adds native code** | It ends OTA for that change and forces a store review |
| ⭐⭐ **Anything touching permissions** | Wrong here is a rejection, and the strings are user-visible |
| **A change to the build config or app.json** | Breaks builds in ways that only appear in EAS |
| **An API contract change** | ⭐ Old app versions never die — this is always a migration |
| **Anything storing data locally** | Secure store vs AsyncStorage is a security decision |
| **A background task or notification** | Both platforms behave differently and both kill you |

```
⭐⭐ THE QUESTION TO ASK OF EVERY PLAN, ON MOBILE:
   "CAN THIS BE FIXED WITH AN OTA UPDATE IF IT IS WRONG?"
   ⇒ ⭐ IF NO, the bar is much higher. A native change that ships
     broken is live for 1–3 days minimum and you cannot recall it.
```

---

# 2 · The session loop

```
 ① PLAN     complex or native ⇒ plan mode, I approve first
 ② COMMIT   checkpoint before anything is written
 ③ BUILD    one feature, small diff
 ④ EXPLAIN  what changed, what to look at, what worried you
 ⑤ REVIEW   /code-review
 ⑥ SECURE   /security-review — auth, data, payments, storage, permissions
 ⑦ ⭐⭐ CROSS Codex second opinion — money, auth, customer data
 ⑧ ⭐⭐ DEVICE **RUN IT ON A REAL PHONE.** §4.
 ⑨ SHIP     only after [10-Ship-Checklist.md](10-Ship-Checklist.md)
```

**Git rules:** commit before every session · `.env` gitignored first · ⭐ the agent never runs
`git push`, `git reset --hard`, `git rebase`, a migration, **or a store submission** · branch
protection on main · secret scanning on.

---

# 3 · ⭐⭐ Prompting for mobile

```
⭐⭐ THE FOUR PATTERNS FROM WEB, PLUS THE MOBILE CONSTRAINT:

 ① CONSTRAINTS, NOT JUST THE GOAL
 ② ⭐⭐ TELL IT WHAT **NOT** TO DO — the line that changes output most
 ③ ⭐ ASK FOR THE FAILURE PATH EXPLICITLY
 ④ MAKE IT REPORT FACTS, NOT REASSURE

 ⑤ ⭐⭐ MOBILE-ONLY: STATE THE PLATFORM CONSTRAINT UP FRONT.

   ❌ "Add a photo upload."
   ✅ "Add a photo upload.
       · Use expo-image-picker. ⭐ NO new native modules — I need to
         keep OTA working.
       · Ask for permission at the moment of use, with a reason string.
         The screen must still work if permission is denied.
       · Resize to max 1200px BEFORE upload and strip EXIF.
       · Show upload progress. Handle: offline, timeout, and the app
         being backgrounded mid-upload.
       · Test path must work on Android 10 as well as iOS."
```

**The starting prompt for a feature:**

> Read `CLAUDE.md`. I want to add \<feature\>. **Do not write code yet.**
> Tell me: which files change · **does this need any native code** · what you are assuming ·
> what you are *not* doing · what I have to do myself · **what happens offline** · and what breaks if
> this is wrong.

---

# 4 · ⭐⭐ The simulator is not evidence

```
⭐⭐ "IT WORKS ON THE SIMULATOR" MEANS ALMOST NOTHING.

  THE SIMULATOR HAS:
    · your Mac's CPU and RAM        ⇒ ⭐ no real memory pressure
    · your wifi                      ⇒ ⭐ no real latency or packet loss
    · no real GPU behaviour          ⇒ jank does not show
    · no real camera, no real GPS, no real push
    · ⭐⭐ NO ANDROID AT ALL, if you only run iOS

  ⇒ ⭐⭐ THE MINIMUM REAL-DEVICE SET:
     · one iPhone
     · ⭐⭐ ONE CHEAP ANDROID — the single most useful testing
       purchase you can make. Everything janks there first.
     · one small screen (SE class)
     · the OLDEST OS VERSION you claim to support
```

```
□ ⭐ EXPO GO IS NOT YOUR APP either — different native modules,
   different behaviour. Build a DEV CLIENT early.
□ Test on mobile data, not wifi
□ ⭐⭐ TEST WITH THE PHONE IN LOW POWER MODE — animations and
   background behaviour change
□ Test with the phone nearly out of storage
```

---

# 5 · ⭐⭐ The OTA question — ask it before adding anything

```
⭐⭐ EAS UPDATE (OTA) CAN CHANGE: JavaScript, styles, images, most
   assets. ⇒ ⭐ A FIX IN FIVE MINUTES.

⭐⭐ IT CANNOT CHANGE: native modules, permissions, app.json/config
   plugins, the icon, the splash, anything in the native project.
   ⇒ ⭐ THOSE NEED A NEW BUILD AND A STORE REVIEW. 1–3 DAYS.

⇒ ⭐⭐ SO EVERY "CAN WE ADD LIBRARY X?" IS REALLY:
   "DOES X ADD NATIVE CODE, AND AM I WILLING TO PAY A STORE REVIEW
    FOR IT?"
   ⭐ MAKE THE AGENT ANSWER THAT BEFORE IT INSTALLS ANYTHING.
```

| ⭐ Rule | |
|---|---|
| **Prefer Expo SDK modules** | Already in your build, no new native code |
| ⭐⭐ **Batch native changes** | If you must add native code, add everything you need in one build |
| **Test the OTA path once, early** | ⭐ Not during an incident |
| ⭐ **Keep a force-update check** | The only kill switch for a broken release |

---

# 6 · ⭐⭐ The Codex cross-check

Same as [web](../03-Web-Developer/09-Testing.md), plus two mobile questions.

```
 "Here is a diff. Find bugs. Check specifically:
  ① Can a logged-in user reach another user's data by changing an ID?
  ② Can this loop, retry, or fire more than once? What does it cost
     in requests AND in battery?
  ③ ⭐⭐ WHAT HAPPENS IF THE NETWORK DROPS HALFWAY THROUGH THIS?
  ④ ⭐⭐ WHAT HAPPENS IF THE OS KILLS THE APP RIGHT HERE?
  ⑤ What input breaks it — empty, null, huge, wrong type, unicode?
  ⑥ Is anything secret or personal written to storage or logs?
  ⑦ Does this behave differently on Android than on iOS?
  List concrete failures with line numbers. Do not summarise the code.
  If you find nothing, say so — do not invent findings."
```

Run it on: money · auth · customer data · uploads · **anything writing to local storage** · anything
touching permissions.

---

# 7 · Which tool for which job

| Job | Use |
|---|---|
| **Complex, risky, or native** | ⭐⭐ **Plan mode** |
| Correctness, simplification | `/code-review` |
| Auth, data, payments, storage, permissions | `/security-review` |
| **Final check on money/auth/data** | ⭐⭐ **Codex** (§6) |
| Searching an unfamiliar codebase | A search subagent |
| Repo rules | `/init` → [CLAUDE-md-template.md](CLAUDE-md-template.md) |
| **Architecture, subtle debugging** | ⭐ The strongest model |
| Bulk mechanical edits | A faster model |

---

# 8 · ⭐⭐ Anti-patterns

| Symptom | Fix |
|---|---|
| "It works" but only on the simulator | ⭐⭐ "Did you run this on a device? Which one?" |
| A library was added that needs native code | ⭐ "Does this add native code? Answer before installing" |
| iOS-only implementation | ⭐⭐ "Test both platforms. Android is half the job." |
| Infinite spinner offline | ⭐ "Every network screen needs an explicit offline state" |
| Battery complaints | ⭐⭐ Find the timer, the poll, or the uncapped retry |
| The form loses everything on return | ⭐ "Save draft state as the user types, not on submit" |
| Crash only on Android 10 | ⭐ Test the oldest OS you support, every release |

```
⭐⭐ THE ONE THAT COSTS THE MOST: "I'VE TESTED IT."
   ⇒ ASK: "ON WHAT DEVICE? WITH WHAT NETWORK?"
   ⇒ PUT IN CLAUDE.md: "Do not claim something is tested if you did
     not run it on a real device. Say 'simulator only'."
```

---

**Back:** [folder index](README.md) · **The memory:** [AGENT-CONTEXT.md](AGENT-CONTEXT.md) ·
**Web equivalent:** [`03-Web-Developer/01-Workflow.md`](../03-Web-Developer/01-Workflow.md)
