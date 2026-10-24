---
name: epic-effects
description: Use when a website, landing page, dashboard or app should look spectacular — epic visual themes, cinematic hero sections, scroll-driven scenes, 3D/WebGL (molecules, particles), shader/aurora/noise backgrounds, glitch, magnetic buttons, custom cursors, page transitions, celebration effects. Gives a theme catalogue, copy-ready effect recipes (Motion, GSAP + ScrollTrigger, Lenis, react-three-fiber, CSS), and the performance/accessibility rules every effect must pass.
---

# Epic Effects

Goal: pages that feel like an Awwwards "Site of the Day", while staying fast, readable and accessible.

## Workflow
1. **Pick a theme** from `references/themes.md` (or blend two). Write the choice and its tokens into
   the app's global CSS before building components. One theme per app; never mix ad hoc.
2. **Choose 1 signature effect + 2–3 supporting effects** from `references/recipes.md`.
   The signature effect lives in the hero; supporting effects repeat across the page for rhythm.
   More than ~4 distinct effects = noise, not epic.
3. **Choreograph**: define the scroll story (hero → reveal → data/proof → climax → CTA) and which
   effect drives each section.
4. **Build**, then **verify**: run the app, take screenshots at 1440px and 375px, light/dark if the
   theme has both, and check the performance budget below.

## Stack (install only what the chosen recipes need)
| Need | Package |
|---|---|
| UI motion, layout/page transitions, gestures | `motion` (Framer Motion; import from `motion/react`) |
| Timelines, scroll scenes, pinning, SVG morph, text split | `gsap` (+ `ScrollTrigger`, `SplitText`; all plugins are free) |
| Smooth scrolling | `lenis` |
| 3D / WebGL | `three`, `@react-three/fiber`, `@react-three/drei`, `@react-three/postprocessing` |
| Celebrations | `canvas-confetti` |
| Lightweight canvas particles (no 3D) | plain Canvas 2D (recipe included), no library |

## Non-negotiable rules
- **Reduced motion**: every effect has a static fallback under `prefers-reduced-motion: reduce`
  (use `useReducedMotion()` from `motion/react` or `gsap.matchMedia()`).
- **Performance budget**: 60fps on a mid-range laptop; animate only `transform`, `opacity`, `filter`
  (sparingly) and shader uniforms. No layout-thrashing properties (`top`, `width`, `box-shadow`) in loops.
  WebGL canvases: `dpr={[1, 1.75]}`, pause when off-screen (`frameloop="demand"` or IntersectionObserver).
  Lazy-load 3D scenes with `React.lazy` + `Suspense` and show a poster/gradient first.
- **Readability first**: text over effects keeps ≥ 4.5:1 contrast — add a scrim/gradient overlay if needed.
- **Mobile**: at ≤ 768px reduce particle counts by ~70%, disable cursor effects, prefer CSS over WebGL.
- **Cleanup**: kill GSAP contexts (`gsap.context(...).revert()` / `useGSAP`), cancel rAF, dispose
  three.js geometries/materials on unmount.
- **Pharma context**: effects serve the message (e.g. molecule = mechanism of action, particles =
  data points, growth line = market share). Never decorate safety/regulatory text with motion.

## References
- `references/themes.md` — 6 complete themes with color tokens, type pairings, signature effects.
- `references/recipes.md` — copy-ready effect code, grouped by category.
