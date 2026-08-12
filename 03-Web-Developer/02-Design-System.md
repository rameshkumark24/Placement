# Phase 02 — Design System

Define the system before generating screens. An agent given tokens produces a consistent app;
an agent without them invents a new blue on every page.

**Gate to pass this phase:** tokens defined in code, component inventory listed, all four states
designed for the core screens.

> Library links and effects catalogue: **[Reference/Component-Libraries.md](Reference/Component-Libraries.md)**

---

## 1. Tokens first

Define these in `tailwind.config.ts` / CSS variables **before** any component exists.

- [ ] Colour: primary, secondary, accent, neutral ramp, semantic (success/warning/error/info)
- [ ] Both light and dark values for every token, if you support dark mode
- [ ] Type scale (a ratio, not arbitrary sizes) and font families
- [ ] Spacing scale (4px or 8px base — stick to it)
- [ ] Border radius scale
- [ ] Shadow scale
- [ ] Breakpoints
- [ ] Z-index scale — named layers, so modals and toasts stop fighting

**Never let a hex code appear in a component.** If the agent writes `#3b82f6`, that's a missing
token. Put it in the config and reference it by name.

---

## 2. Component inventory

List what you need before building. shadcn/ui covers almost all of it — copy in only what you use.

**Forms** — input · textarea · select · combobox · checkbox · radio · switch · slider · date picker · file upload
**Feedback** — toast · alert · dialog · drawer/sheet · tooltip · popover · progress · skeleton
**Data** — table · card · list · badge · avatar · pagination · tabs · accordion
**Navigation** — navbar · sidebar · breadcrumbs · dropdown menu · command palette
**Layout** — container · grid · stack · separator

---

## 3. Wireframe before styling

- [ ] Every screen wireframed before any styling
- [ ] Mobile layouts **drawn, not assumed** — mobile-first if your users are mobile
- [ ] Onboarding / first-run experience designed
- [ ] Navigation structure settled — how does a user get anywhere from anywhere

---

## 4. The four states — for every screen

| State | What it needs |
|---|---|
| **Loading** | Skeleton matching the real content shape, not a centred spinner |
| **Empty** | Explanation + a primary action. This is a new user's first impression. |
| **Error** | What went wrong in plain language + a retry button. Never a raw error code. |
| **Success** | The actual content |

Plus: **partial** (some data, some failed) and **offline**, if either is realistic.

---

## 5. Microcopy

Write the words before the agent invents them.

- [ ] Button labels — verbs describing the outcome ("Create account", not "Submit")
- [ ] Error messages — specific and actionable ("Password needs 8+ characters", not "Invalid")
- [ ] Empty state copy — what this is, and what to do first
- [ ] Confirmation dialogs — state exactly what will happen
- [ ] Destructive actions get a confirmation; **irreversible ones get a typed confirmation**
- [ ] Loading copy for anything over ~3 seconds

---

## 6. Accessibility, decided here not retrofitted

- [ ] Colour contrast ≥ **4.5:1** for body text, 3:1 for large text (WCAG AA)
- [ ] Never encode meaning in colour alone — add an icon or text
- [ ] Focus states visible and designed (never `outline: none` without a replacement)
- [ ] Touch targets ≥ 44×44px
- [ ] Semantic HTML planned — real `<button>`, `<nav>`, `<main>`, heading hierarchy in order
- [ ] Form labels tied to inputs; errors announced, not just coloured red
- [ ] `prefers-reduced-motion` respected in every animation

Retrofitting accessibility costs several times what designing it in costs. Contrast in particular
is nearly free now and expensive to fix after 40 components exist.

---

## 7. Motion budget

Effects are performance and accessibility costs. Pick a few deliberately.

- Animate **`transform` and `opacity` only** — animating `width`/`top`/`height` forces layout on
  every frame
- Durations: 150ms for micro-interactions, 300ms for transitions, longer feels broken
- Background video: muted, `playsinline`, poster image, and **never on mobile**
- Particle/canvas backgrounds are GPU-expensive — test on a mid-range Android first

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Phase gate

- [ ] Tokens in the config, no hex codes in components
- [ ] Component inventory listed
- [ ] Core screens wireframed with all four states
- [ ] Contrast checked on the palette
- [ ] Microcopy written for the critical path
