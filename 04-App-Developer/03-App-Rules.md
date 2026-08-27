# 📲 App Rules — loops, listeners, offline and the OS

> ⭐⭐ **On mobile a loop costs you money *and* costs the user battery and data — and they can see
> both in Settings.** A one-star review saying "drains my battery" is very hard to recover from. This
> file is the rules that prevent it.

---

# 1 · ⭐⭐ The ten-question audit

Run against any AI-generated diff.

| # | Question |
|---|---|
| 1 | ⭐⭐ Is there **any** API call in `render()`? |
| 2 | Does every fetching `useEffect` have correct **primitive** deps? |
| 3 | ⭐⭐ Does `refetchOnWindowFocus` fire on every app foreground? |
| 4 | Is every listener removed — AppState, NetInfo, keyboard, navigation, dimensions? |
| 5 | Is every `setInterval` / timer cleared? |
| 6 | Is every in-flight request cancelled on unmount? |
| 7 | Do retries have a max, backoff **and jitter**? |
| 8 | ⭐ Is there any polling that keeps running in the background? |
| 9 | Are payment and queued-write operations idempotent? |
| 10 | Is there a spend cap with an alert below it? |

---

# 2 · ⭐⭐ The loops that cost battery

```
⭐⭐ ① THE RENDER LOOP
  💀 useEffect(() => { fetch(...).then(setData); });   ⭐ no deps
  ⇒ fetch → setState → render → fetch → forever.
  ⭐ On web this is a bill. ⭐⭐ ON MOBILE IT IS ALSO A DEAD BATTERY
    AND A DATA BILL THE USER PAYS.

⭐⭐ ② THE FOCUS-REFETCH LOOP — MOBILE-ONLY, AND EASY TO MISS
  ⭐ refetchOnWindowFocus on web means "tab focused". ON MOBILE IT
    FIRES ON EVERY APP FOREGROUND.
  ⇒ ⭐⭐ A USER SWITCHING BETWEEN APPS TRIGGERS A REQUEST STORM.
  ⇒ set a staleTime, or turn it off and refresh deliberately.

⭐⭐ ③ THE TUNNEL RETRY STORM
  ⭐ Underground, every request fails. An uncapped retry loop now
    hammers a dead network for the whole journey.
  ⇒ max 3 · exponential backoff · ⭐ JITTER · and ⭐⭐ STOP RETRYING
    WHEN NetInfo SAYS OFFLINE. Wait for connectivity instead.

⭐⭐ ④ BACKGROUND POLLING
  ⭐ Both platforms will punish you, and users see it in battery
    settings. ⇒ ⭐⭐ USE PUSH NOTIFICATIONS. Not a timer.
```

---

# 3 · ⭐⭐ Listeners do not unmount with the screen

```
⭐⭐ THIS IS THE MOBILE-ONLY LEAK. THEY ARE GLOBAL SUBSCRIPTIONS.

  □ AppState.addEventListener        ⇒ remove it
  □ NetInfo.addEventListener         ⇒ remove it
  □ Keyboard.addListener             ⇒ remove it
  □ Dimensions.addEventListener      ⇒ remove it
  □ navigation.addListener           ⇒ remove it
  □ Linking.addEventListener         ⇒ remove it
  □ any BackHandler subscription     ⇒ remove it
  □ ⭐ every setInterval / setTimeout ⇒ clear it

  ⇒ ⭐⭐ NAVIGATE TO A SCREEN TEN TIMES AND YOU HAVE TEN LISTENERS,
    EACH RETAINING ITS WHOLE CLOSURE. The app gets slower the longer
    it is used, and the cause is invisible.

  ⭐ THE TEST: navigate into and out of a screen 20 times, then watch
    memory. Flat is correct. Climbing is a leak.
```

---

# 4 · ⭐⭐ Offline is a design decision, per screen

```
⭐⭐ THREE ANSWERS. PICK ONE PER SCREEN, IN ADVANCE.

 ① ⭐ WORKS OFFLINE     ⇒ cached reads, writes queue for later
 ② ⭐ READS OFFLINE     ⇒ cached data + a "last updated" stamp;
                          writes blocked with a clear message
 ③ ⭐ NEEDS NETWORK     ⇒ an explicit offline state, NOT a spinner

⭐⭐ AN INFINITE SPINNER ON A LOST CONNECTION IS THE MOST COMMON
   MOBILE BUG THERE IS.
```

```
□ ⭐⭐ "CONNECTED" IS NOT "THE INTERNET WORKS".
   ⭐ A captive-portal wifi resolves DNS and blocks everything. NetInfo
     says connected. Every request hangs.
   ⇒ ⭐ CHECK isInternetReachable, and give every request a timeout.
□ ⭐ QUEUED WRITES ARE IDEMPOTENT — they WILL send twice.
   ⭐⭐ Generate the key when the user acts, not when the request is
     sent, or a retry becomes a duplicate.
□ Show what is unsynced. ⭐ Never pretend it saved when it did not.
□ ⭐ THE QUEUE SURVIVES THE APP BEING KILLED — persist it
□ Cap the queue. ⭐⭐ A user offline for a week must not return with
   4,000 queued requests that all fire at once.
□ ⭐ TEST: airplane mode · then a THROTTLED LOSSY connection, which is
   WORSE, because requests hang instead of failing
```

---

# 5 · ⭐⭐ The OS can kill you at any moment

```
⭐⭐ ANDROID KILLS BACKGROUNDED APPS FREELY. iOS DOES TOO, LESS OFTEN.
   ⭐ YOUR APP DOES NOT GET A WARNING.

□ ⭐⭐ SAVE DRAFT STATE AS THE USER TYPES, NOT ON SUBMIT.
   ⭐ Coming back to an empty form after taking a phone call is the
     fastest way to lose a user.
□ ⭐ HANDLE AppState background → foreground:
   refresh stale data, ⭐⭐ BUT DO NOT STAMPEDE every query at once
□ ⭐ A LONG TASK MUST SURVIVE BACKGROUNDING, or tell the user to stay
   on screen. ⭐⭐ AN UPLOAD THAT SILENTLY DIES IS WORSE THAN ONE THAT
   REFUSES TO START.
□ ⭐ DEEP LINKS WORK FROM A COLD START, not just when already running
□ Restore navigation state where it makes sense
□ ⭐⭐ THE DRILL: rotate · background for 10 minutes · force-kill and
   reopen · run out of storage · deny every permission
```

---

# 6 · State & data

```
□ ⭐⭐ ANYTHING YOU CAN COMPUTE MUST NOT BE STORED. Two copies of one
   fact drift, and nothing tells you.
□ ⭐ SERVER DATA IS A CACHE, NOT STATE. Use a query library with
   persistence — ⭐⭐ AND THAT PERSISTENCE MEANS YOU HAVE WRITTEN USER
   DATA TO DISK. Decide where (→ [05-Security.md §1](05-Security.md)).
□ ⭐ NEVER USE THE ARRAY INDEX AS A LIST KEY. It moves state onto the
   wrong record when the list changes.
□ Paginate everything. ⭐ No screen loads an unbounded list.
□ ⭐ MONEY AS INTEGERS. Dates UTC, formatted at render.
```

---

# 7 · Forms

```
□ ⭐⭐ THE KEYBOARD MUST NOT COVER THE INPUT OR THE SUBMIT BUTTON.
   ⭐ KeyboardAvoidingView behaves DIFFERENTLY on iOS and Android.
     TEST BOTH. This is the most-shipped mobile layout bug.
□ ⭐ CORRECT keyboardType AND autoComplete per field — email, tel,
   numeric, one-time-code. It costs nothing and users notice.
□ ⭐ A WAY TO DISMISS THE KEYBOARD
□ Validate on blur, then on change once a field has errored
□ ⭐⭐ NEVER CLEAR THE FORM ON ERROR
□ ⭐ SERVER ERRORS LAND ON THE RIGHT FIELD
□ ⭐⭐ DOUBLE-SUBMIT: disabling the button is UX. The fix is an
   idempotency key + a unique constraint.
□ ⭐ SAVE THE DRAFT AS THEY TYPE (§5)
```

---

# 8 · Notifications

```
□ ⭐ ASK FOR PERMISSION AT A MOMENT WHEN THE VALUE IS OBVIOUS, never
   on first launch. ⭐⭐ ASKED TOO EARLY = DENIED = GONE FOREVER,
   because re-asking is not possible.
□ ⭐ EXPLAIN WHY BEFORE THE SYSTEM PROMPT — a pre-prompt screen you
   control, so a "no" there costs you nothing
□ ⭐⭐ A NOTIFICATION MUST DEEP-LINK TO THE RIGHT SCREEN, from a cold
   start. ⭐ Landing on the home screen is a broken notification.
□ Handle: app foreground · background · killed. ⭐ All three differ.
□ ⭐ LET USERS CONTROL CATEGORIES IN-APP. One irrelevant push and
   they turn off all of them.
□ ⭐⭐ NO PII IN THE NOTIFICATION BODY — it shows on a locked screen.
```

---

# 9 · The basics an agent will skip

```
⭐⭐ IT BUILDS THE FEATURE. IT DOES NOT ADD THESE UNLESS ASKED.

 □ ⭐ THE OFFLINE STATE
 □ ⭐ THE LISTENER CLEANUP
 □ ⭐⭐ THE KEYBOARD-COVERS-THE-BUTTON CASE
 □ safe-area handling
 □ ⭐ THE ANDROID BACK BEHAVIOUR
 □ pagination on the list it just built
 □ ⭐⭐ WHAT HAPPENS ON THE SECOND TAP
 □ the empty and error states
 □ ⭐ WHAT HAPPENS IF THE PERMISSION IS DENIED
 □ ⭐⭐ WHAT HAPPENS IF THE OS KILLS THE APP MID-FLOW

⭐ PUT THESE IN CLAUDE.md AS A "BEFORE YOU FINISH" LIST.
```

---

**Back:** [folder index](README.md) · **Security:** [05-Security.md](05-Security.md) ·
**Audit:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
