---
name: master-web-craft
description: "Complete web design and engineering skill. Builds non-generic production UIs, adds purposeful motion, audits and fixes performance and responsiveness, and creates 3D/WebGL experiences. Modes: Build, Motion, Audit, Improve, 3D."
---

# Master Web Craft

A synthesis of the best web design and engineering practices. Four operating modes — detect the right one from the request, or ask when ambiguous.

---

## STEP 0: Detect Mode

| Signal | Mode |
|--------|------|
| "build", "create", "design", "make a page/component", "new UI" | **Build** |
| "animate", "add motion", "transition", "micro-interaction", "hover" | **Motion** |
| "audit", "review", "score", "what's wrong with", "prospect", "client report" | **Audit** |
| "fix", "improve", "lighthouse", "performance", "responsive", "optimize" | **Improve** |
| "3D", "WebGL", "Three.js", "GSAP", "scroll-driven", "immersive" | **3D** |
| Ambiguous | Ask: Build / Motion / Audit / Improve / 3D? |

---

## MODE 1 — BUILD

### The Taste Gate (run before writing any code)

Every design decision must pass this gate. Generic output is a failure.

**Before generating UI, answer:**
1. What is the dominant visual personality? (minimal, brutalist, editorial, art-deco, warm, technical)
2. What makes this different from a default shadcn/Tailwind scaffold?
3. What will users remember?

If you can't answer all three, ask the user before proceeding.

**Anti-slop rules — enforce strictly:**
- No default Tailwind gray palettes without intentional reasoning
- No `rounded-lg` everywhere — border radius communicates personality
- No generic hero with centered H1 + subtext + CTA unless the context demands it
- No identical hover states on every interactive element
- No "card grid on white background" as the default layout
- Typography must have hierarchy — not just size, but weight, spacing, and contrast
- Every shadow must earn its place — shadows express depth, not decoration
- Spacing must follow a system — don't mix arbitrary px values

### Frontend Quality Principles

**Typography**
- Establish a type scale: display / heading / body / caption — each with distinct weight and size
- Use `font-display: swap` and preload key typefaces
- Body text: 16–18px minimum, line-height 1.5–1.7, max-width 65–75ch
- Fluid type: prefer `clamp()` over breakpoint jumps

**Colour**
- Define roles: primary, surface, muted, accent, destructive — use tokens not raw values
- Contrast ratios: 4.5:1 minimum for normal text, 3:1 for large text (WCAG AA)
- Dark and light mode from the start — not bolted on later

**Layout**
- CSS Grid for two-dimensional layout, Flexbox for one-dimensional alignment
- 8px base unit — all spacing multiples of 4 or 8
- Avoid fixed widths — use `max-width` + `width: 100%`

**Component architecture**
- Design tokens first, then components
- No one-off styles — if you write it twice, make it a token
- States: default, hover, focus, active, disabled — all of them, every time

**Accessibility (non-negotiable)**
- Every `<img>` has `alt`
- Every form input has a `<label>`
- Every interactive element is keyboard-reachable
- Focus ring is visible and styled — never `outline: none` without a replacement
- Semantic HTML: headings in order, landmarks (`<main>`, `<nav>`, `<header>`, `<footer>`)
- ARIA roles only when semantic HTML is insufficient

---

## MODE 2 — MOTION

### Designer Weighting

Before writing a single animation, establish the project context and apply the matching designer lens.

**The Three Lenses:**

| Designer | Philosophy | Ask |
|----------|-----------|-----|
| Emil Kowalski (Linear, ex-Vercel) | Restraint and speed — motion should be invisible | "Should this animate at all?" |
| Jakub Krehel (jakub.kr) | Subtle production polish — details that feel shipped | "Is this subtle enough for production?" |
| Jhey Tompkins (@jh3yy) | Playful experimentation — CSS and creativity | "What could this become?" |

**Context-to-lens mapping:**

| Project Type | Primary | Secondary | Selective |
|-------------|---------|-----------|-----------|
| Productivity tool (dashboard, SaaS) | Emil | Jakub | Jhey (onboarding only) |
| Marketing / landing page | Jakub | Jhey | Emil (nav, forms) |
| Creative portfolio | Jakub | Jhey | Emil (high-freq interactions) |
| Kids / educational | Jakub | Jhey | Emil (game loops) |
| E-commerce | Jakub | Emil | Jhey (product showcase) |
| Mobile app | Jakub | Emil | Jhey (delighters) |

### The Frequency Gate

Before adding any animation, determine how often users trigger it:

| Frequency | Rule |
|-----------|------|
| Rare (monthly) | Expressive, delightful motion welcome |
| Occasional (daily) | Subtle and fast |
| Frequent (100s/day) | No animation or instant transition |
| Keyboard-initiated | Never animate |

### Duration Guidelines

| Context | Duration |
|---------|---------|
| Productivity UI (Emil-primary) | Under 300ms — 180ms ideal |
| Production polish (Jakub-primary) | 200–500ms |
| Creative / playful (Jhey-primary) | Whatever serves the effect |

Never use the same duration everywhere. Smaller elements animate faster.

### Motion Rules

- Start from `scale(0.9)`, never `scale(0)` — natural entry
- Enter and exit are not mirrors — exits should be subtler (smaller translate, quicker)
- Never animate `width`, `height`, `top`, `left` — use `transform` and `opacity` only
- Custom `cubic-bezier` or spring — never bare `ease` or `ease-in-out`
- Use CSS transitions (not keyframes) for anything the user can retrigger rapidly — keyframes can't retarget mid-flight
- `transform-origin` matters — dropdowns expand from their trigger, not center
- `will-change` is a targeted hint, not a global fix

### Accessibility (mandatory in every animation)

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Every animation generated must include this. No exceptions. Include it in the same code block, not as a follow-up.

Avoid vestibular triggers: large-scale zoom, spinning, parallax — gate all of them behind `prefers-reduced-motion`.

No looping attention-seeking motion: pulsing dots, breathing CTAs, throbbing indicators. Static treatments only unless the user explicitly requests otherwise.

---

## MODE 3 — AUDIT

Choose the audit type based on the goal:

| Goal | Audit Type |
|------|-----------|
| Business / sales / client report | Prospect Audit |
| Motion and animation review | Motion Audit |
| Performance and Core Web Vitals | Performance Audit |
| Accessibility and SEO scoring | Lighthouse Audit |
| Cross-breakpoint layout | Responsive Check |

### Prospect Audit (business language, non-technical audience)

1. Load the URL in a browser (requires Playwright MCP or Chrome DevTools MCP)
2. Screenshot desktop (full viewport) and mobile (375×812)
3. Score 1–10 across: Visual Design, UX Clarity, Performance, Mobile, Messaging, Trust Signals
4. Map findings to actionable recommendations — no jargon
5. Deliver in plain business language: "Your hero takes 4.2s to load on mobile — you're losing conversions"

### Motion Audit

1. Identify all animations on the page
2. Apply designer lens (establish context first, then weight Emil / Jakub / Jhey)
3. Check each animation against the Frequency Gate
4. Check `prefers-reduced-motion` support
5. Check for vestibular triggers, looping animations, layout-property animations
6. Report per-designer with severity: Pass / Warn / Fail + specific fix

### Performance Audit (Core Web Vitals)

**Baseline targets:**

| Metric | Target |
|--------|--------|
| LCP | ≤2.5s |
| CLS | ≤0.1 |
| INP | ≤200ms |
| FCP | ≤1.8s |
| TTFB | ≤800ms |

**Diagnose by metric:**

- **LCP slow**: identify LCP element → optimize (WebP/AVIF, `<link rel="preload">`, `fetchpriority="high"`, explicit dimensions)
- **CLS failing**: find shifting elements → add `width`/`height`, `aspect-ratio`, `font-display: swap`
- **INP slow**: find long tasks → code-split, defer JS, use `requestIdleCallback`, debounce handlers
- **TTFB slow**: CDN, server caching, redirect elimination, compression

Fix order: Delivery → Critical path → Assets → Layout stability → Interactivity. Re-measure after each round.

### Responsive Check

Test at: 320px, 375px, 428px, 768px, 1024px, 1280px, 1440px, 1920px.

Check for: horizontal overflow, overlapping elements, content cut off, navigation unusable, touch targets < 44px, font < 16px on mobile, images without `aspect-ratio`.

Mobile first — fix 320px before desktop. Never hide content on mobile — restructure it.

### Lighthouse Audit (100/100/100)

Run Lighthouse → parse failed audits → fix → rebuild → re-run. Loop until 100.

Fix order per score:
- **Accessibility**: contrast, alt text, labels, heading order, `lang` attribute, ARIA
- **Best Practices**: HTTPS, no vulnerable libraries, no console errors, CSP
- **SEO**: meta description, canonical, robots.txt, descriptive link text, structured data

Always run mobile first, then verify desktop separately.

---

## MODE 4 — IMPROVE

Improve is an iterative loop: Diagnose → Recommend → Fix → Verify.

1. **Diagnose** — run Audit (pick relevant type) + visual critique (hierarchy, colour, spacing, typography, emotion)
2. **Recommend** — map findings to fix commands, deduplicate, order by severity, present to user for approval
3. **Fix** — apply in this order:
   - Structure first: layout, spacing, responsiveness
   - Systems: design tokens, consistency, resilience
   - Visual: type, colour, copy clarity
   - Personality: boldness/quietness, delight, motion
   - Ambitious: 3D effects, advanced CSS, scroll-driven
4. **Verify** — re-audit, compare before/after, report delta
5. **Loop** — two rounds is normal. Rarely need more than three.

Never fix without diagnosing. Never skip user approval on the recommendation list. Always run a final polish pass after fixes.

---

## MODE 5 — 3D

Use when building immersive, scroll-driven, or WebGL experiences.

### Library Selection

| Need | Library |
|------|---------|
| General 3D scene | Three.js |
| React integration | React Three Fiber (@react-three-fiber) |
| Animation + scroll-driven | GSAP + ScrollTrigger |
| React animation | Framer Motion / Motion |
| Enterprise 3D | Babylon.js |
| Procedural animation | react-spring (physics) |

### Three.js Essentials

- Always set `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))` — cap at 2, not unlimited
- Use `BufferGeometry`, never legacy `Geometry`
- Dispose of geometries, materials, and textures on unmount — memory leaks are silent on desktop, fatal on mobile
- `OrbitControls` for dev/debug; remove or restrict for production
- Prefer `draco` compression for large GLTF assets

### GSAP + ScrollTrigger

- `ScrollTrigger.refresh()` after layout changes — stale scroll positions cause jank
- Use `gsap.context()` for React cleanup
- `scrub: true` for scroll-linked motion, `scrub: 1` (seconds) for smooth lag
- Pin sections with `pin: true` only when the effect genuinely needs it — pinning is expensive
- Always provide a `prefers-reduced-motion` path: `gsap.matchMedia()`

```javascript
const mm = gsap.matchMedia();
mm.add("(prefers-reduced-motion: no-preference)", () => {
  // full animation
});
mm.add("(prefers-reduced-motion: reduce)", () => {
  // instant or no animation
});
```

### Performance in 3D

- `requestAnimationFrame` loop — never `setInterval`
- Frustum culling is automatic in Three.js — don't fight it
- Use `InstancedMesh` for repeated geometry (100+ identical objects)
- LOD (Level of Detail) for distant objects
- Profile with `stats.js` or Chrome DevTools GPU — measure before optimizing

---

## Core Checklist (all modes)

Before marking any output as complete:

- [ ] Taste gate passed — no generic slop
- [ ] Semantic HTML — correct heading order, landmarks, labels
- [ ] Keyboard accessible — every interactive element reachable and operable
- [ ] Focus ring visible — never removed without replacement
- [ ] Colour contrast — 4.5:1 normal text, 3:1 large text
- [ ] `prefers-reduced-motion` respected — every animation gated
- [ ] Responsive — tested mentally at 320px, 768px, 1440px minimum
- [ ] Performance — no layout-triggering animations, images have dimensions
- [ ] Design tokens — no magic numbers

---

## Attribution

This skill synthesises techniques and workflows from the following open-source projects. All are used in compliance with their respective licences.

| Source | Author | Licence | Contribution |
|--------|--------|---------|--------------|
| [taste-skill](https://github.com/Leonxlnx/taste-skill) | Leonxlnx | MIT | Anti-slop taste principles, design intentionality rules |
| [design-motion-principles](https://github.com/kylezantos/design-motion-principles) | Kyle Zantos | MIT | Three-designer motion framework, frequency gate, motion accessibility |
| [webdesign-agency-skills](https://github.com/peterhadorn/webdesign-agency-skills) | Peter Hadorn | Apache 2.0 | Audit/improve loops, Lighthouse workflow, performance audit, responsive check, prospect audit |
| [claudedesignskills](https://github.com/freshtechbro/claudedesignskills) | freshtechbro | MIT | 3D/WebGL patterns, Three.js, GSAP/ScrollTrigger, React Three Fiber |
| [claude-skills (frontend-design)](https://github.com/anthropics/skills) | Anthropic | MIT | Frontend quality principles, production-grade UI standards |
| [agent-skills](https://github.com/vercel-labs/agent-skills) | Vercel Labs | MIT | Web design guidelines, React/Next.js best practices |
| [claude-skills](https://github.com/jeffallan/claude-skills) | jeffallan | MIT | Full-stack skill structure and patterns |
| [claude-skills (jezweb)](https://github.com/jezweb/claude-skills) | jezweb | MIT | Cloudflare, React, Tailwind v4, design-loop patterns |

**Apache 2.0 notice (Peter Hadorn / webdesign-agency-skills):** The audit, improve, lighthouse, performance, and responsive workflows in this skill are derived from the original work. Changes were made to synthesise these workflows into a unified skill structure. The original work is licensed under the Apache License, Version 2.0.

Original copyright notices are preserved in `ATTRIBUTION.md`.
