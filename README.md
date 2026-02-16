# 🚀 ASCEND v1.3.8 — Cinematic Loading Screen + 3 New Themes

## Overview

v1.3.8 is a polish and visual identity release. The loading screen has been fully rebuilt as a cinematic 4-phase sequence, and three new color themes join the palette: Nature, Mono, and Earth.

---

## 🎬 Cinematic Loading Screen (v1.3.8 Overhaul)

The 2.25s single-animation screen has been replaced with a 3.6s orchestrated sequence across 4 distinct phases.

### Phase Timeline

| Phase | Time | What happens |
|-------|------|--------------|
| 1 — Star field | 0–0.9s | 80 stars materialise at randomised positions, sizes, and delays |
| 2 — Wordmark | 0.6–1.4s | "ASCEND" letters drop in one by one with blur-to-sharp entrance |
| 3 — Countdown + Launch | 1.4–2.6s | Rocket on pad; 3→2→1→GO countdown; rocket shakes then launches |
| 4 — Exhaust + Exit | 2.6–3.6s | Exhaust trail grows behind rocket; bar completes; screen fades |

### Animations: rocketSit, rocketShake, padGlow, thrusterFlame, rocketLaunch, exhaustGrow, cdTick, cdGo, starAppear, letterDrop, taglineIn, padIn, loaderBar (5-keyframe eased), statusIn

### Status Line
Cycles: Initialising systems → Loading habit data → Syncing goals → Preparing dashboard → Ready for launch → Launching

---

## 🎨 New Themes (13 Total)

**Nature** — `#0b130d` bg / `#5a9e6f` accent — forest floor, moss, bark
**Mono** — `#0a0a0a` bg / `#e8e8e8` accent — true black, near-white, ash grays
**Earth** — `#150e0a` bg / `#c4703a` accent — dark clay, burnt terracotta, warm sand

All three are light/dark mode compatible.

---

## 🧪 Test Checklist

**Loading screen:**
- [ ] Star field appears on refresh
- [ ] ASCEND letters drop in one by one
- [ ] Countdown: 3 → 2 → 1 → GO
- [ ] Rocket shakes, then launches upward
- [ ] Exhaust trail behind rocket
- [ ] Status cycles through messages
- [ ] Fades at ~3.6s, app interactive after

**New themes:**
- [ ] Nature / Mono / Earth swatches in Settings grid
- [ ] Each theme applies correct accent color throughout
- [ ] Light mode readable on all 3

**Regression:**
- [ ] All 10 original themes work
- [ ] Goal card buttons render (v1.3.7)
- [ ] Area badges show full labels (v1.3.7)
- [ ] All 7 nav SVG icons intact

---

## Changelog

```
v1.3.8 — Cinematic Loading Screen + 3 New Themes
feat: 4-phase cinematic loading screen (3.6s)
feat: 80-star JS-generated field with randomised delays
feat: letter-by-letter wordmark entrance with blur-to-sharp
feat: launchpad SVG scene
feat: 3-2-1-GO countdown animations
feat: rocketShake tremor before launch
feat: padGlow thruster glow
feat: exhaustGrow trail on launch
feat: status line with 5-message cycle
feat: Nature theme (forest green / moss)
feat: Mono theme (true black / near-white)
feat: Earth theme (dark clay / terracotta)
```

**Version:** 1.3.8 | **Release:** February 2026
**Next:** v1.3.9 — Social Hub (friend codes, leaderboard, feed)
