# 🔧 ASCEND v1.3.1 - Bug Fixes & Polish

## 🎯 Overview

v1.3.1 is a focused patch release. No new pages, no scope creep.  
Three bugs squashed, two quality-of-life features shipped.  
ASCEND is now tighter, cleaner, and more professional.

---

## 🐛 Bugs Fixed

### 1. Ghost Theme List in Sidebar
**What happened:** The old v1.2.2 sidebar theme cards were never removed from the HTML during the v1.3 migration — they were only hidden by CSS. When the sidebar collapsed, the `overflow: hidden` stopped clipping correctly and all 10 theme names leaked out below the sidebar boundary as plain text with checkmarks.

**Root cause:** During v1.3 development, the Settings theme grid was added correctly but the original `sidebar-footer` block with `theme-selector` HTML was never deleted — it lived at line ~929 as a ghost node in the DOM.

**Fix:** Deleted the entire duplicate `sidebar-footer` block from the HTML. One footer, one job: show the current theme name hint.

---

### 2. Analytics Insight Cards Overflowing
**What happened:** The 4th insight card (Journal Streak) was being cut off on the right side. The grid was forcing `repeat(4, 1fr)` via an inline `style` attribute that overrode the CSS.

**Root cause:** When the 4th card was added in v1.3, the inline style `style="grid-template-columns: repeat(4, 1fr)"` was added directly to the HTML element, hardcoding 4 equal columns regardless of viewport width.

**Fix:** Removed the inline style override. Grid now uses CSS: `repeat(2, 1fr)` — a clean 2×2 layout that's readable, professional, and responsive.

**Before → After:**
```
Before: [card1][card2][card3][card4→cut off]
After:  [card1][card2]
        [card3][card4]
```

---

### 3. "Most Consistent" Showing 200%+ Success Rate
**What happened:** The Most Consistent insight card was showing habit names pulled from journal prompts and displaying mathematically impossible success rates like "200% success rate."

**Root cause (two bugs):**
1. `Object.keys(h.history).length` was counting ALL history entries (both completed and missed days), not just completions
2. No cap was applied — if a habit was young (created recently) but had many entries from a date migration, the rate exceeded 100%

**Fix:**
```js
// Before (broken)
const comp = Object.keys(h.history).length;
const rate = comp / totalDays;

// After (correct)
const comp = Object.values(h.history).filter(v => v === true || v === 1).length;
const rate = Math.min(1, comp / totalDays); // hard cap at 100%
```

---

## ✨ New Features

### 4. Collapsed Sidebar — Icon-Only Mode

The collapsed sidebar was showing only the Analytics icon and blank space. Now when collapsed it shows a clean, professional icon-only navigation.

**What it looks like:**
- All 6 nav icons centered vertically in their rows
- Proper padding and spacing — icons don't crowd each other
- Active state (accent color) still visible on the current page icon
- Expand button lives inside the nav for easy access when collapsed

**Tooltip system:**
- Hover any collapsed nav icon → label tooltip appears to the right
- Tooltips use `data-tooltip` HTML attribute + CSS `::after` pseudo-element
- No JavaScript required — pure CSS, instant response
- Tooltip disappears when sidebar is expanded (irrelevant)

**Technical implementation:**
```css
.sidebar.collapsed .nav-item::after {
    content: attr(data-tooltip);
    position: absolute;
    left: calc(var(--sidebar-collapsed) + 8px);
    /* ... fade in on hover */
}
```

---

### 5. Light / Dark Mode Toggle

**Location:** Settings → Appearance (top of section, above theme grid)

**How it works:**
- Animated pill switch (🌙 dark ↔ ☀️ light)
- Toggle slides the knob, switch background changes color
- Label updates: "Dark Mode" / "Light Mode"
- Persists to `localStorage` — remembered across sessions and page reloads
- Works with all 10 color themes

**What light mode changes:**
```
--bg-primary:    dark bg → #f0f4f8 (cool light gray)
--bg-secondary:  dark card → #ffffff (white cards)
--bg-tertiary:   dark elevated → #e2e8f0
--text-primary:  light text → #0f172a (near black)
--text-secondary: → #475569
--glass-bg:      dark glass → rgba(255,255,255,0.75)
--glass-border:  glow border → rgba(0,0,0,0.08)
```

**What light mode preserves:**
- Your selected accent color (Ocean cyan, Sakura pink, etc. all stay vibrant)
- All component layouts and spacing
- Chart.js graphs (re-render on mode switch)

**Technical implementation:**
```js
// Brightness stored separately from theme
localStorage: 'ascend-brightness' = 'dark' | 'light'

// Applied as HTML attribute alongside data-theme
document.documentElement.setAttribute('data-brightness', brightness);

// CSS override block
[data-brightness="light"] {
    --bg-primary: #f0f4f8;
    --bg-secondary: #ffffff;
    // ...
}
```

This means theme + brightness are fully independent. You can have Ocean Light, Ocean Dark, Sakura Light, Sakura Dark — all 20 combinations work.

---

## 📊 What Changed from v1.3

| Area | v1.3 | v1.3.1 |
|------|-------|---------|
| Sidebar (collapsed) | Analytics icon only | All 6 icons + tooltips |
| Sidebar (footer) | Ghost theme list leaking | Clean theme hint only |
| Analytics grid | 4 cards cut off | 2×2 clean grid |
| Most Consistent | Shows prompt text, 200% | Correct habit + capped % |
| Brightness | Dark only | Dark + Light toggle |
| Settings | Theme grid only | Brightness toggle + theme grid |

---

## 🗄️ Storage

No new storage keys introduced. Additions:

```
localStorage key: 'ascend-brightness'
Values: 'dark' | 'light'
Default: 'dark'
```

All existing keys unchanged — full backward compatibility.

---

## 🧪 Test Checklist (8 min)

**Phase 1: Sidebar (2 min)**
- [ ] Collapse sidebar → all 6 icons visible and centered
- [ ] Hover each collapsed icon → tooltip appears to the right
- [ ] Active page icon shows accent color when collapsed
- [ ] Expand button in nav works
- [ ] No theme names or text bleeding outside sidebar

**Phase 2: Analytics (2 min)**
- [ ] Navigate to Analytics
- [ ] All 4 insight cards visible in a 2×2 grid
- [ ] No card is cut off
- [ ] Most Consistent shows a real habit name (not prompt text)
- [ ] Success rate shows a value between 0%–100%

**Phase 3: Light/Dark Mode (3 min)**
- [ ] Settings → Appearance → toggle switch visible
- [ ] Click toggle → app switches to light mode
- [ ] Switch knob animates, label says "Light Mode"
- [ ] All 10 themes look correct in light mode
- [ ] Reload page → light mode persists
- [ ] Toggle back → dark mode restores
- [ ] Charts re-render correctly in both modes

**Phase 4: Regression (1 min)**
- [ ] Journal page still works
- [ ] Calendar journal dots still appear
- [ ] Theme switching still works in Settings
- [ ] Habit completion still saves

**If all pass → Deploy!** 🚀

---

## 💡 Design Notes

### Why 2×2 for Insight Cards?
Four cards in a single row requires very wide screens to be readable. At 1200px each card is only ~250px wide — not enough room for the value text. A 2×2 grid gives each card ~45% width, plenty of breathing room, and a clear visual hierarchy. It also scales gracefully to mobile (2×2 → 1×4).

### Why Separate Brightness from Theme?
Putting light/dark as a property of the theme (e.g., "Ocean Light" vs "Ocean Dark" as separate themes) would double the theme count and confuse the UI. Separating them as independent axes (10 themes × 2 brightnesses = 20 combinations) is cleaner and mirrors how design systems actually work (e.g., Tailwind dark mode, iOS appearance settings).

### Why CSS `data-brightness` Instead of a Class?
Using `document.documentElement.setAttribute('data-brightness', ...)` mirrors exactly how `data-theme` works in the same file. Consistency. It also means both attributes can be read at a glance in DevTools: `<html data-theme="ocean" data-brightness="light">`.

---

## 📝 Changelog

```
v1.3.1 (Bug Fixes & Polish)
fix: removed ghost sidebar theme card HTML from v1.2.2 that was leaking outside sidebar
fix: removed inline style forcing 4-column analytics grid, replaced with responsive 2×2
fix: Most Consistent calculation now counts only true completions, rate capped at 100%
feat: collapsed sidebar now shows all nav icons centered with hover tooltips
feat: light/dark mode toggle in Settings → Appearance
feat: brightness persists to localStorage independently of theme
```

---

## 🚀 Deployment

```bash
git add ascend-v1.3.1.html ASCEND-v1.3.1-README.md
git commit -m "v1.3.1 - Bug fixes, light mode, collapsed sidebar icons"
git push origin main
```

---

**Version:** 1.3.1  
**Type:** Patch (bug fixes + polish)  
**Release:** February 2026  
**Philosophy:** Ship it right, not just fast.
