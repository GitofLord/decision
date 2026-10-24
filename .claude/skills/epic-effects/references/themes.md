# Theme Catalogue

Each theme: mood, tokens (paste into `:root` / `[data-theme]`), fonts (Google Fonts), signature effect,
supporting effects. Recipe names refer to `recipes.md`.

---

## 1. Clinical Glass — premium, trustworthy, bright
Best for: pharma corporate sites, HCP portals, executive dashboards.
```css
:root {
  --bg: #f4f7fb; --surface: rgba(255,255,255,.62); --border: rgba(15,40,80,.10);
  --text: #0c1a2b; --muted: #5b6b80;
  --accent: #2563eb; --accent-2: #14b8a6; --glow: rgba(37,99,235,.28);
  --radius: 20px; --blur: 18px;
}
[data-theme="dark"] { --bg:#070d18; --surface:rgba(18,28,46,.55); --border:rgba(255,255,255,.08);
  --text:#e8eef7; --muted:#8b9ab0; --glow:rgba(56,130,255,.35); }
```
Fonts: **Plus Jakarta Sans** (UI) + **Instrument Serif** (hero accent words).
Signature: `aurora-gradient` behind frosted cards. Supporting: `reveal-stagger`, `count-up`, `tilt-card`.

## 2. Cyberpunk Lab — neon, energetic, gamified
Best for: gamified sales competitions, leaderboards, launch events.
```css
:root {
  --bg: #07060d; --surface: #110f1c; --border: #2a2540;
  --text: #f1eefe; --muted: #9a93b8;
  --accent: #ff2bd6; --accent-2: #00f0ff; --accent-3: #faff00; --glow: rgba(255,43,214,.45);
  --radius: 6px;
}
```
Fonts: **Space Grotesk** + **JetBrains Mono** (numbers, labels).
Signature: `glitch-text` headline + `grid-horizon`. Supporting: `neon-border`, `scramble-text`, `confetti-burst`.

## 3. Dark Luxury — cinematic, editorial, high-end
Best for: brand/product launch sites, KOL event pages, annual reports.
```css
:root {
  --bg: #0b0a09; --surface: #151311; --border: #2b2620;
  --text: #f3ede4; --muted: #a39887;
  --accent: #c9a96e; --accent-2: #8a6f4a; --glow: rgba(201,169,110,.25);
  --radius: 2px;
}
```
Fonts: **Cormorant Garamond** (display) + **Inter Tight** (body).
Signature: `split-text-reveal` + `smooth-scroll` + `pinned-scroll-story`. Supporting: `image-mask-reveal`, `magnetic-button`, `custom-cursor`.

## 4. Bioluminescent — organic, scientific, mesmerizing
Best for: mechanism-of-action stories, R&D pages, molecule explorers.
```css
:root {
  --bg: #020b10; --surface: rgba(6,30,40,.6); --border: rgba(80,255,220,.12);
  --text: #dffcf5; --muted: #7fb3a8;
  --accent: #3dffc8; --accent-2: #4f7bff; --accent-3: #b26bff; --glow: rgba(61,255,200,.4);
  --radius: 28px;
}
```
Fonts: **Sora** + **IBM Plex Mono**.
Signature: `molecule-3d` with bloom. Supporting: `particle-field`, `noise-gradient`, `reveal-stagger`.

## 5. Retro-Futur — Y2K chrome, playful, bold
Best for: training/quiz apps, internal campaigns, onboarding.
```css
:root {
  --bg: #f2efe9; --surface: #ffffff; --border: #111111;
  --text: #111111; --muted: #555555;
  --accent: #ff5b1f; --accent-2: #3a5bff; --accent-3: #ffd23f;
  --radius: 14px; --shadow-hard: 6px 6px 0 #111;
}
```
Fonts: **Unbounded** (display) + **DM Sans**.
Signature: `marquee-band` + chunky `press-button` (hard shadow). Supporting: `sticker-pop`, `confetti-burst`, `scramble-text`.

## 6. Data Cosmos — deep space, analytical, grand
Best for: national sales maps, big-number storytelling, analytics landing pages.
```css
:root {
  --bg: #03040a; --surface: rgba(14,18,36,.7); --border: rgba(140,160,255,.14);
  --text: #eef1ff; --muted: #8e97c2;
  --accent: #7c8cff; --accent-2: #ff8a5c; --glow: rgba(124,140,255,.35);
  --radius: 16px;
}
```
Fonts: **Manrope** + **Space Mono**.
Signature: `starfield-warp` hero → data appears as constellations. Supporting: `count-up`, `spotlight-cursor`, `pinned-scroll-story`.

---

### Blending rule
Take tokens from one theme, at most one effect from a second. Example: Clinical Glass tokens +
Bioluminescent `molecule-3d` hero = "premium science" launch page.
