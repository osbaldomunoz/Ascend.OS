# ✨ ASCEND v1.3.5 — Analytics Fix + Animation System

## Overview

v1.3.5 fixes the analytics trend chart bug and introduces a full animation system across every page. ASCEND now feels alive.

---

## 🐛 Bug Fixed: Analytics Trend Chart

**What you saw:** The completion trend line started at 0% for the first several days, then spiked to near 100%, creating a fake hill shape that didn't reflect actual usage.

**Root cause:** Days before any habits were created were being treated as "0% completion" instead of being skipped. If you created habits on Feb 10, every day from Feb 1–9 was pushing `0` into the chart — pulling the average down and making the eventual real data look like a spike.

**Fix — three changes:**
1. **`anyActivity` flag** — each chart day checks if any habit existed yet. If none did, `null` is pushed instead of `0`
2. **`spanGaps: false`** — Chart.js now cleanly skips null points rather than interpolating through them
3. **Day normalization** — `setHours(0,0,0,0)` ensures habit creation dates are compared at midnight, not mid-day, preventing off-by-one errors on the creation day itself

**Before → After:**
```
Before: 0% 0% 0% 0% 0% 0% 97% 95% 80%  ← fake spike
After:  [gap] [gap] [gap] [gap] 97% 95% 80%  ← honest line
```

---

## ✨ Animation System

### Page Transitions
Every page fades in and slides up 16px when navigated to. Smooth, not flashy. Uses `cubic-bezier(0.22, 1, 0.36, 1)` — the same easing Apple uses in iOS.

```css
@keyframes pageEnter {
    from { opacity: 0; transform: translateY(16px); }
    to   { opacity: 1; transform: translateY(0); }
}
```

### Staggered Card Pop-ins
Stat cards, goal cards, insight cards, and habit items stagger their entrance with 50ms delays between each one — creating a cascade effect instead of everything appearing at once.

```css
.goal-card:nth-child(1) { animation-delay: 0.05s; }
.goal-card:nth-child(2) { animation-delay: 0.10s; }
/* ... up to 6 */
```

### Progress Bar Fill
Goal progress bars animate from 0% width to their actual value when the Goals page loads. 0.8s ease, 0.3s delay so the bar starts filling after the card appears.

### Milestone Bounce
Checking a milestone makes the circle bounce — scales to 0, springs to 1.25×, settles at 1. Instant tactile feedback without being distracting.

### Habit Completion Ripple
Completing a habit triggers an expanding ring around the item — a ripple that fades out over 0.5s. Makes every checkoff feel satisfying.

### Toast Slide-in / Slide-out
Toasts now slide up from below and pop into view. When dismissed, they slide back down instead of just vanishing. The `.hiding` class triggers the exit animation 280ms before removal from the DOM.

### Micro-interactions
- **Nav items:** `translateX(3px)` on hover — a subtle nudge right when expanded, scale bump when collapsed
- **Buttons:** `scale(0.96)` on `:active` — physical press feel
- **Goal cards:** More pronounced hover lift (`translateY(-5px) scale(1.01)`) with enhanced shadow
- **Settings theme cards:** Scale up on hover, scale up more when active (selected)
- **Journal textarea:** Focus glow using accent color at 15% opacity

### Page Title Entrance
`page-title` and `page-subtitle` each have their own `fadeInUp` entrance with a 70ms offset between them.

### Accessibility
```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.01ms !important;
    }
}
```
Users who have "Reduce Motion" enabled in their OS settings get instant transitions instead of animations. No one is excluded.

---

## 🧪 Test Checklist (5 min)

**Analytics fix:**
- [ ] Navigate to Analytics
- [ ] Completion Trend line only shows data from when you started adding habits
- [ ] No artificial 0% days before your first habit
- [ ] Gap/break in line where no habits existed (not a fake 0)

**Animations:**
- [ ] Navigate between pages → each page slides up into view
- [ ] Go to Dashboard → stat cards pop in one by one
- [ ] Go to Goals → goal cards appear in cascade, progress bars fill after cards appear
- [ ] Check a milestone → circle bounces
- [ ] Complete a habit → ripple ring appears
- [ ] Any action → toast slides up from bottom, slides back down when dismissed
- [ ] Hover nav items → subtle rightward nudge
- [ ] Click any button → slight scale-down press feel
- [ ] Hover goal cards → lift + shadow

**Regression:**
- [ ] All 7 pages navigate correctly
- [ ] Goals still save and load
- [ ] Journal auto-save still works
- [ ] Theme switching still works

---

## 📝 Changelog

```
v1.3.5 — Analytics Fix + Animation System
fix: trend chart now pushes null for days before any habits existed
fix: spanGaps: false prevents Chart.js from interpolating through null gaps
fix: habit creation date normalized to midnight to prevent off-by-one errors
feat: pageEnter animation on every page transition
feat: staggered cardPop entrance for stat/goal/insight cards and habit items
feat: progress bar fill animation (0 → actual %) on Goals page load
feat: milestone check bounce animation
feat: habit completion ripple animation
feat: toast slide-in from below + animated slide-out
feat: nav item hover nudge (expanded: right, collapsed: scale)
feat: button active scale press effect
feat: goal card enhanced hover lift
feat: journal textarea focus glow
feat: page title/subtitle staggered entrance
feat: prefers-reduced-motion media query disables all animations for accessibility
```

---

**Version:** 1.3.5
**Type:** Bug fix + Polish
**Release:** February 2026
**Feel:** The app should feel as good as it looks.
