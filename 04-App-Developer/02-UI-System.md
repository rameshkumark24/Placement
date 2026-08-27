# 🎨 UI System — native-feeling, not "AI-generated"

> ⭐⭐ **A mobile app that looks like a website in a phone frame is the tell.** Web-styled buttons,
> hover states that do nothing, a hamburger menu instead of tabs, and no platform conventions at all.
> **Users notice immediately, even if they cannot name why.**

---

# 1 · ⭐⭐ The two tells — and both matter

| ⭐ Tell #1: it looks AI-generated | Do instead |
|---|---|
| Purple→blue gradient header | Flat, one accent used sparingly |
| Cards with emoji icons | Real icons, platform-appropriate |
| "Empower your workflow" | What it does, in the user's words |
| Everything centred, huge padding | Density that suits a phone |
| A fake person's testimonial | Nothing, until you have a real one |

| ⭐⭐ Tell #2: it looks like a website | Do instead |
|---|---|
| A hamburger menu as primary nav | ⭐ **Bottom tabs** (iOS + Android both) |
| Hover states | ⭐⭐ There is no hover. **Pressed states.** |
| A web-style top nav bar | Native header, with a real back affordance |
| `<div>`-thinking layout | ⭐ Flex, `Pressable`, platform components |
| Tiny tap targets | ⭐ 44pt / 48dp minimum |
| A "Back to top" button | ⭐ Tap the status bar (iOS) does this free |
| Text you cannot select but looks selectable | Match the affordance to reality |

---

# 2 · ⭐⭐ Platform conventions — do not force one onto the other

```
⭐⭐ THE RULE: RESPECT WHERE IT MATTERS, UNIFY WHERE IT DOESN'T.

  ⭐ RESPECT (users notice immediately):
    · NAVIGATION — iOS swipes back from the left edge. Android has a
      system back gesture/button. ⭐⭐ BOTH MUST WORK.
    · SHARE — iOS share sheet vs Android share intent
    · DATE/TIME PICKERS — use the native ones
    · ⭐ HAPTICS — iOS users expect them; use them sparingly
    · Typography defaults — SF vs Roboto proportions differ

  ⭐ UNIFY (nobody cares):
    · your brand colour, your spacing scale, your icon set
    · your own components' look

  ⭐⭐ THE ONE PEOPLE GET WRONG: **ANDROID BACK.**
    ⭐ If the system back button does not do the obvious thing —
      close the modal, go up one screen, exit at the root — the app
      feels broken to every Android user. Test it on every screen.
```

---

# 3 · Tokens first

```
⭐⭐ ONE SCALE. NOTHING ELSE ALLOWED. This alone is most of what makes
   a UI look designed.

  spacing:  4 · 8 · 12 · 16 · 24 · 32
  type:     13 · 15 · 17 · 22 · 28      ⭐ mobile sizes, not web's
  radius:   one value, maybe two
  colour:   ⭐⭐ NAMED BY ROLE, NEVER BY VALUE
            fg · fgMuted · bg · bgSubtle · border · primary · danger
```

```
□ ⭐ TYPE MUST SCALE WITH THE SYSTEM SETTING. Do not hardcode absolute
   sizes and ignore accessibility scaling.
   ⭐⭐ TEST AT THE LARGEST SYSTEM FONT SIZE — text must WRAP, not clip.
□ ⭐ CONTRAST 4.5:1 for body text. Grey-on-grey fails outdoors, which
   is where phones actually get used.
□ Dark mode from the token block, or disabled deliberately
□ ⭐⭐ NEVER a fixed pixel height on anything containing text
```

---

# 4 · The eight components

```
⭐⭐ BUILD THESE, STOP. This is a design system for one app.

 ① Button       — variant × size × pressed × disabled × loading
 ② Input        — ⭐ WITH label, error text and keyboard type built in
 ③ Card / ListItem
 ④ Badge        — ⭐⭐ your state machine, visible
 ⑤ Skeleton     — the loading shape
 ⑥ ⭐⭐ EmptyState — icon + message + ACTION. Twenty minutes, and the
                    most-noticed component you will write.
 ⑦ ⭐⭐ ErrorState — message + retry, branched by cause (incl. OFFLINE)
 ⑧ Sheet/Modal  — ⭐ from a library. Gesture handling and safe areas
                    are genuinely hard.

 ⭐ PLUS: Stack, Row — your layout primitives.
```

**Every component needs its states:** default · **pressed** (⭐⭐ not hover — there is no hover) ·
disabled · loading (⭐ hold the width so nothing jumps) · focused (for keyboard/TV users).

```
⭐⭐ USE Pressable, NOT TouchableOpacity, AND GIVE IT A REAL PRESSED
   STATE. ⭐ A button that does not visibly respond to a tap feels
   broken — and on a slow device the delay is long enough to notice.
   ⭐ Add hitSlop to anything small.
```

---

# 5 · ⭐⭐ The four states — plus offline

```
① LOADING  ⇒ a skeleton shaped like the content
② ERROR    ⇒ what happened + what to do + RETRY, branched by cause
③ EMPTY    ⇒ ⭐⭐ TWO STATES: never-had-any (+ the action that creates
             the first) vs filtered-to-zero (+ clear filter)
④ SUCCESS  ⇒ ⭐ VISIBLE CONFIRMATION. Silence reads as failure and
             people tap twice.
⑤ ⭐⭐ OFFLINE ⇒ **THE MOBILE-ONLY FIFTH STATE.** Not an error — a
             designed state. ⭐ An infinite spinner on a lost
             connection is the most common mobile bug there is.
```

---

# 6 · ⭐⭐ Prompting for mobile UI

```
❌ "Make a modern home screen for a fitness app."

✅ "Build the home screen.
    BASE: React Native primitives + our components in src/ui.
      Pressable, not TouchableOpacity. FlashList for the list.
    LAYOUT: header with the greeting and avatar; a horizontal
      scroll of today's sessions; a vertical list below.
      SafeAreaView. Bottom tabs are the primary nav — NO hamburger.
    COLOUR: one accent (#___) on the primary action only. Neutrals
      otherwise. Flat — no gradient.
    TYPE: our token scale. Must scale with the system font setting.
    STATES: build loading (skeleton), empty (two variants), error
      with retry, and OFFLINE.
    PLATFORM: Android system back must work on every screen.
    COPY: real strings, no placeholders.
    DO NOT: no gradients, no glassmorphism, no emoji icons, no hover
      states, no web-style top nav, no fixed pixel heights on text."
```

⭐⭐ **The "DO NOT" block is the line that changes the output most.**

---

# 7 · Performance is a design constraint here

```
□ ⭐⭐ LISTS ARE VIRTUALISED. FlashList or FlatList — never .map()
   over 500 rows. ⭐ It renders all 500 at once and janks.
□ ⭐⭐ NEVER A FULL-RESOLUTION PHOTO IN A LIST. Resize server-side,
   cache locally. ⭐ The #1 cause of jank and memory crashes on Android.
□ ⭐ ANIMATIONS ON THE NATIVE DRIVER (useNativeDriver / Reanimated).
   ⭐⭐ A JS-driven animation stutters the moment anything else runs.
□ Keep animation short — 150–250ms. ⭐ Long transitions make a fast
   app feel slow.
□ ⭐ NO SHADOW ON EVERY ITEM in a long list on Android — it is
   expensive. Use elevation sparingly.
□ ⭐⭐ TEST SCROLL ON A CHEAP ANDROID. It is where jank shows first.
□ Respect reduce-motion
```

---

# 8 · Accessibility

```
□ ⭐ EVERY INTERACTIVE ELEMENT HAS accessibilityLabel AND
   accessibilityRole
□ ⭐⭐ TAP TARGETS 44pt / 48dp, not adjacent
□ ⭐⭐ TEST AT THE LARGEST SYSTEM FONT SIZE — text wraps, never clips
□ Contrast 4.5:1
□ ⭐ COLOUR IS NEVER THE ONLY SIGNAL
□ ⭐ TRY IT WITH VOICEOVER / TALKBACK ONCE. Ten minutes, and it
   finds real problems.
□ Focus moves sensibly when a modal opens and closes
□ Respect reduce-motion and bold-text settings
```

---

**Back:** [folder index](README.md) · **Components:**
[Reference/Component-Libraries.md](Reference/Component-Libraries.md) ·
**Audit:** [10-Ship-Checklist.md](10-Ship-Checklist.md)
