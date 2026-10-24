---
name: pharma-app-studio
description: Use when building or extending a pharma / sales analytics web app, dashboard, or gamified training/sales tool — e.g. rep performance dashboards, territory analytics, KOL maps, product launch trackers, quiz/leaderboard apps for medical reps. Provides the domain model, KPI definitions, gamification patterns and the visual bar the app must meet.
---

# Pharma App Studio

## 1. Domain model (start here)
Core entities: `Product` (molecule, brand, ATC class), `Territory/Region`, `Rep` (medical representative),
`HCP` (doctor; specialty, segment A/B/C, potential), `Pharmacy`, `Visit` (call), `Sale` (units, value, period),
`Target`, `Competitor`.

## 2. KPI dictionary
| KPI | Formula | Note |
|---|---|---|
| Target achievement % | actual / target × 100 | color: <90 red, 90–100 amber, ≥100 green |
| Market share % | own units / market units × 100 | show trend vs previous period |
| Evolution index (EI) | (1 + own growth) / (1 + market growth) × 100 | >100 = outgrowing market |
| Growth % | (this − last) / last × 100 | YTD and MAT variants |
| Call frequency | visits / HCP / period | by segment |
| Coverage % | visited HCPs / target HCPs × 100 | |
| Productivity | sales / rep | |

Always pair a number with: trend (sparkline), comparison (target or prior period), and an insight sentence.

## 3. Analytics → decision
Every dashboard needs an "Insights" panel that states: what changed, likely driver (decomposition by
region/product/HCP segment), and a recommended action. Use Pareto (80/20), cohort, and variance analysis
where relevant; flag outliers with z-score.

## 4. Gamification patterns
- XP & levels tied to real behaviours (coverage, target %, quiz accuracy) — never to raw clicks.
- Badges with tiers (bronze/silver/gold), streaks, weekly leaderboard with team + individual views.
- Progress rings and "next milestone" hint; celebration animation (confetti / glow) on achievement.
- Fairness: normalise leaderboards by territory potential so reps compete on effort, not luck.

## 5. Visual bar
- Hero KPI cards with animated count-up, gradient accent, subtle glass layer.
- Page transitions and staggered card entrance (Framer Motion), skeleton loaders.
- Maps (Turkey provinces / regions) as choropleth for territory views.
- Light/dark themes, reduced-motion support, 375px mobile layout.

## 6. Done checklist
- [ ] Runs locally, no console errors
- [ ] Screenshot verified in light and dark
- [ ] KPIs match the dictionary above
- [ ] Insight panel present
- [ ] Mock data is realistic and fictional
