# 🎨 UI Component & Animation Libraries

Curated build resources. Pick a **primary** library and stick to it — mixing three design systems
is the fastest way to a site that looks assembled rather than designed.

---

## Primary component libraries

### shadcn/ui — *start here*
- **URL:** https://ui.shadcn.com/docs/components
- **Best for:** login pages, signup forms, dashboards, admin panels, tables
- **Components:** buttons, forms, dialogs, tables, navigation menus, toasts, sheets
- **Why it's the default:** components are copied *into* your repo, not installed. You own the code,
  the agent can edit it, and there's no upstream version to fight.

### HeroUI
- **URL:** https://www.heroui.com/docs/guide/introduction
- **Best for:** hero sections, landing page components
- **Components:** headers, CTAs, feature blocks

### Origin UI / Aceternity
- Larger prebuilt marketing blocks when you want a landing page fast

---

## Animation & motion

### ReactBits
- **URL:** https://reactbits.dev/get-started/index
- **Best for:** text animations, background themes, card animations
- **Components:** animated typography, interactive backgrounds, motion effects

### Motion Sites
- **URL:** https://motionsites.ai/
- **Best for:** motion-heavy hero sections

### Vibe Code Components
- **URL:** https://vibecodecomponents.com/
- **Blog:** https://blog.vibecoder.me/

### Design Spells
- **URL:** https://designspells.com/
- **Best for:** micro-interaction ideas — the small details that make a site feel considered

### Motion (Framer Motion)
- The underlying animation library for most of the above. Learn `whileInView` and `layout` and you
  can build most scroll effects yourself.

---

## Fonts & assets

| Resource | URL | Purpose |
|---|---|---|
| WhatTheFont | https://www.myfonts.com/pages/whatthefont | Identify a font from a screenshot |
| Google Fonts | https://fonts.google.com | Free web fonts (use via `next/font`) |
| Fontshare | https://fontshare.com | Free quality display fonts |
| Lucide | https://lucide.dev | Icon set that pairs with shadcn/ui |
| unDraw / Storyset | https://undraw.co | Free illustrations |
| Unsplash / Pexels | https://unsplash.com | Free photography |
| Coolors | https://coolors.co | Palette generation |
| Realtime Colors | https://realtimecolors.com | Preview a palette on a real layout |

---

## Effects catalogue

Tick what the design actually calls for. Every effect costs performance and accessibility — respect
`prefers-reduced-motion`, and don't ship all of them.

### Scroll effects
- [ ] Smooth scrolling
- [ ] Parallax scrolling
- [ ] Parallax storytelling
- [ ] Scroll-triggered animations
- [ ] Stack scroll effect

### Interactive effects
- [ ] Hover force effects
- [ ] Cursor animations
- [ ] Liquid glass effect
- [ ] Particle sphere
- [ ] Paper image effect

### Background effects
- [ ] Background video
- [ ] Animated gradients
- [ ] Particle backgrounds
- [ ] Geometric patterns

### Motion & transitions
- [ ] Page transitions
- [ ] Element entrance animations
- [ ] Carousel / slider animations
- [ ] Modal animations

### Special
- [ ] Confetti / fireworks
- [ ] Form hero section
- [ ] Storytelling animations
- [ ] Loading animations / preloaders

---

## Navigation & interaction components

**Navigation:** main menu · dropdown · mobile hamburger · mega menu · search bar · sticky header · breadcrumbs

**Interactive:** hover animations · scroll animations · page transitions · cursor effects · button effects · loading animations

---

## Performance rules for effects

- Background video: muted, `playsinline`, poster image, and **never** on mobile
- Particle backgrounds are GPU-expensive — test on a mid-range Android before committing
- Animate `transform` and `opacity` only; animating `width`/`top` forces layout on every frame
- Lazy-load anything below the fold
- Always honour `prefers-reduced-motion`:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```
