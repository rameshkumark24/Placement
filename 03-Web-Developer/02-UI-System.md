# 🎨 UI System — modern, not "AI-generated"

> ⭐⭐ **The problem this file solves: default AI output all looks the same, and everyone can see it.**
> Purple gradient hero, three feature cards with emoji icons, "Empower your workflow", glassmorphism,
> everything centred. It is not ugly — it is *anonymous*, and it reads as "nobody decided anything."

**The fix is not better taste. It is constraint plus specificity.** shadcn/ui for the base you own,
[reactbits.dev](https://reactbits.dev/) for the one moment that needs motion, and prompts that say
what **not** to do.

---

# 1 · ⭐⭐ The AI-design tells — and what to do instead

| ⭐ The tell | ⭐⭐ Do this instead |
|---|---|
| **Purple→blue gradient background** | One flat background. Colour goes on ONE element that matters |
| **Glassmorphism / frosted cards** | A real border, a real background, a real shadow — or nothing |
| **Three feature cards with emoji icons** | Real icons (lucide, 20px, stroke 1.5) — and maybe not three cards at all |
| **Everything centred in a 1200px column** | Asymmetry. Something bleeding off an edge. A real focal point |
| **"Empower your workflow" / "Seamlessly integrate"** | What it actually does, in the words a user would use |
| **A testimonial from a person who does not exist** | Nothing, until you have a real one |
| **Animation on every element** | ⭐ One deliberate effect. Motion everywhere is motion nowhere |
| **`font-bold` everywhere, default system font** | One real typeface, two weights, real hierarchy |
| **Six shades of the brand colour** | A neutral ramp does 90% of the work + one accent |
| **A hero image from an AI generator** | A real screenshot of the actual product |

```
⭐⭐ THE SINGLE BEST TEST:
   Screenshot your page. Screenshot the first result for "AI landing page".
   ⭐ IF THEY HAVE THE SAME SHAPE, YOU HAVE NOT DESIGNED ANYTHING.
```

---

# 2 · shadcn/ui — the base

```
⭐⭐ WHY shadcn AND NOT A COMPONENT LIBRARY:
   ⭐ IT COPIES THE CODE INTO YOUR REPO. You own it. You can edit it.
   ⇒ no fighting a library's opinions, no wrapper layers, no upgrade
     that changes your buttons.
   ⇒ ⭐⭐ AND IT IS BUILT ON RADIX, SO FOCUS TRAPPING, KEYBOARD
     NAVIGATION AND ARIA ARE ALREADY CORRECT — which is the part you
     would get wrong by hand.
```

**The rules:**

```
□ ⭐ EDIT THE COMPONENTS. They are your files. Do not wrap a shadcn
   Button in your own Button that adds three props.
□ ⭐⭐ CHANGE THE DEFAULT THEME IMMEDIATELY. The default is a tell too.
   Set your radius, your neutrals, your one accent, your font.
□ Take the ones that are genuinely hard: Dialog, Dropdown, Combobox,
   Popover, Sheet, Toast, Tooltip.
   ⭐ These solve focus trapping and keyboard behaviour. That is what
     you are actually taking.
□ ⭐ DO NOT take a Table and try to make it generic. Build the one
   table this app needs.
□ Keep the set small. ⭐⭐ EIGHT COMPONENTS IS A DESIGN SYSTEM FOR ONE
   APP: Button, Input, Card, Badge, Skeleton, EmptyState, ErrorState,
   Dialog. Plus three layout primitives: Stack, Row, Grid.
```

**Tokens first, always** — before any component:

```css
:root {
  /* spacing — ONE scale, nothing else allowed */
  --space-1: 4px;  --space-2: 8px;  --space-3: 12px;
  --space-4: 16px; --space-6: 24px; --space-8: 32px;
  /* type — five sizes, not eleven */
  --text-sm: 14px; --text-base: 16px; --text-lg: 18px;
  --text-xl: 24px; --text-2xl: 32px;
  /* colour — ⭐⭐ NAMED BY ROLE, NEVER BY VALUE */
  --fg: #111827;  --fg-muted: #6b7280;
  --bg: #ffffff;  --bg-subtle: #f9fafb;  --border: #e5e7eb;
  --primary: #2563eb;  --danger: #dc2626;
  --radius: 6px;
}
```

> ⭐⭐ **One spacing scale is why a page looks professional.** Padding of 13px here and 7px there —
> nobody consciously notices, everybody feels it as "off". ⭐ And naming colours by role
> (`--danger`) rather than value (`--red-600`) means dark mode is a second token block, not a rewrite.

---

# 3 · ⭐⭐ reactbits.dev — motion, used once

**[reactbits.dev](https://reactbits.dev/)** — copy-paste React components for animation and effects.
Same model as shadcn: **the code comes into your repo and you own it.**

```
⭐⭐ THE RULE THAT MAKES IT WORK: ONE EFFECT PER PAGE. MAYBE TWO.
   ⭐ Motion everywhere reads as a template. ONE deliberate effect on
     the element that deserves attention reads as craft.
   ⇒ ⭐⭐ ASK: "WHAT IS THE ONE THING I WANT THEM TO NOTICE?"
     That element gets the effect. Nothing else does.
```

| ⭐ Use it for | ⭐⭐ Not for |
|---|---|
| The hero headline — one text effect | Every heading on the page |
| One background element on the landing page | The background of every section |
| A hover state on the primary CTA | Every card and every link |
| A reveal on one key section | Every section scrolling in |
| A loading/transition moment | Replacing a skeleton |

```
□ ⭐ RESPECT prefers-reduced-motion. Wrap effects so they turn off.
   Large animation causes real nausea for some people — and it is one
   media query.
□ ⭐⭐ CHECK THE COST. Some effects run a canvas or a rAF loop forever.
   On a landing page that is battery on a phone. Profile it.
□ Keep durations short — 150–250ms for UI, longer only for a hero.
   ⭐ A slow transition makes a fast app feel slow.
□ Never animate something the user is trying to read or click.
□ ⭐ Do not put an effect above the fold that delays the LCP element.
```

---

# 4 · ⭐⭐ Prompting for UI

```
❌ THE PROMPT THAT PRODUCES THE GENERIC RESULT:
   "Make a modern landing page for a SaaS product."

✅ THE PROMPT THAT DOESN'T:

  "Build the hero section.
   BASE: shadcn Button and Card. Tailwind. lucide icons, 20px, stroke 1.5.
   LAYOUT: asymmetric — headline + CTA on the left at 55% width,
     product screenshot on the right, bleeding off the right edge.
     Generous whitespace. Left-aligned, not centred.
   COLOUR: one accent (#2563eb) on the primary CTA only. Everything
     else from the neutral ramp. Flat background.
   TYPE: <font> at 600 for the headline, 400 for body. Two weights only.
   MOTION: ONE reactbits <effect> on the headline. Nothing else animates.
   COPY: headline is '<my real headline>'. Sub is '<my real sub>'.
     No placeholder text anywhere.
   DO NOT: no gradient background, no glassmorphism, no emoji icons,
     no three-card feature row, no fake testimonials, no centred column."
```

| ⭐ The five things a UI prompt must contain | |
|---|---|
| **Layout structure** | Where things sit, and the proportions |
| **⭐⭐ The DO NOT list** | **The line that changes the output most** |
| **Where the ONE effect goes** | Otherwise it animates everything |
| **Real copy** | Or you get placeholder text you will forget to replace |
| **The base components** | shadcn, so it does not invent its own button |

---

# 5 · Every screen, four states

**Not negotiable, and the agent will build one of the four.** Full detail:
[10-Ship-Checklist.md §4](10-Ship-Checklist.md).

```
① LOADING  ⇒ skeleton SHAPED LIKE THE CONTENT. Not a centred spinner
              replaced by something a different size — that is layout
              shift. ⭐ Do not show it under 200ms.
② ERROR    ⇒ what happened + what to do + RETRY. Branch by status.
              ⭐⭐ NEVER CLEAR THE FORM.
③ EMPTY    ⇒ ⭐⭐ TWO STATES: never-had-any (+ the button that creates
              the first) vs filtered-to-zero (+ clear filters).
④ SUCCESS  ⇒ ⭐ VISIBLE CONFIRMATION. Silence reads as failure and
              people click twice.
```

---

# 6 · The eight decisions that make it look designed

```
⭐⭐ NO TASTE REQUIRED. DO THESE EIGHT.

 ① ⭐⭐ WHITESPACE. Amateur UIs are cramped. Double what feels right.
 ② ⭐ ALIGNMENT. One misaligned element is the most visible flaw there is.
 ③ ⭐ CONTRAST. Body text near-black on white.
    ⭐⭐ Grey-on-grey is both an accessibility failure and looks weak.
 ④ ONE ACCENT COLOUR, on the primary action only.
 ⑤ ⭐⭐ MAX LINE LENGTH 65–75 characters. Full-width text instantly
    marks a page as unstyled.
 ⑥ CONSISTENT RADIUS AND BORDER WEIGHT.
 ⑦ ⭐⭐ VISIBLE FOCUS RINGS. :focus-visible. Never outline:none.
 ⑧ ⭐ HOVER + ACTIVE + DISABLED on everything interactive.
    ⭐⭐ A button with no hover state feels broken even when it works.
```

---

# 7 · Accessibility — the part that is nearly free

```
□ ⭐⭐ SEMANTIC ELEMENTS. <button> for actions, <a href> for navigation.
   ⭐ A div with onClick is not focusable, not in tab order, has no
     Enter/Space, and announces as nothing.
   ⭐⭐ THE FIX IS DELETING CODE, NOT ADDING ARIA.
□ Every input has a <label>. ⭐ A placeholder is not a label.
□ Icon-only buttons get aria-label
□ ⭐ VISIBLE FOCUS on everything reachable
□ Colour is never the only signal — a red border needs text too
□ ⭐⭐ TAB THROUGH THE WHOLE PAGE WITH NO MOUSE. Five minutes. This
   finds more than any tool.
□ ⭐ aria-live on async results ("3 results found") — otherwise a
   screen reader user gets no signal anything happened
□ prefers-reduced-motion respected
```

> ⭐ **The first rule of ARIA is don't use ARIA.** Every attribute is a promise you must implement.
> `role="button"` on something not focusable advertises a door with no handle.

---

**Back:** [folder index](README.md) · **Components reference:**
[Reference/Component-Libraries.md](Reference/Component-Libraries.md) · **The audit:**
[10-Ship-Checklist.md](10-Ship-Checklist.md)
