# 🚀 ASCEND v1.3.7 — Goal Card Bug Fixes

## Overview

v1.3.7 is a targeted bug-fix release. Three regressions introduced during the v1.3.6 emoji→SVG migration broke goal card rendering. All three are resolved.

---

## 🐛 Bug Reports & Root Cause Diagnoses

### Bug 1 — Goal card action buttons missing (trophy / edit / delete)

**Symptom:** The three action buttons in the top-right corner of each goal card were invisible.

**Root cause:** During the v1.3.6 SVG migration, the SVG markup strings were embedded directly inside JS template literals using `&quot;` HTML entities to escape the inner double-quotes. This looked like:

```js
`<button ...><svg xmlns=&quot;http://...&quot; ...></svg></button>`;
```

When this string is assigned to `innerHTML`, the browser's HTML parser decodes `&quot;` → `"` — but by then the attribute quoting structure is broken. The parser sees an SVG whose attributes are not properly quoted in the original source string context, so the tags are either skipped or rendered as raw text nodes. The buttons ended up as invisible or zero-size elements.

**Fix:** Extracted all three SVG icons into a `GOAL_SVG` constant object defined once above `renderGoalCard`, using JavaScript escaped single-quotes (`\'`) for SVG attribute values:

```js
const GOAL_SVG = {
  trophy: "<svg ... stroke='currentColor' ...>...</svg>",
  edit: "<svg ...>...</svg>",
  trash: "<svg ...>...</svg>",
};
```

The template literal then uses `${GOAL_SVG.trophy}` etc. — clean variable interpolation with no entity encoding issues.

---

### Bug 2 — Area badge shows single letter ("H", "R") instead of full label

**Symptom:** The area badge on each goal card displayed only a single letter — `H` for Health, `R` for Relationships, etc.

**Root cause:** In v1.3.6, the `AREA_META` object's `icon` field was changed from emoji (e.g. `❤️`) to single-letter stubs (`'H'`, `'M'`, `'R'`, etc.) as a placeholder during the SVG migration. The goal card badge template was:

```js
<span class="goal-area-badge">
  ${area.icon} ${area.label}
</span>
```

Combined with the `text-transform: uppercase` CSS on `.goal-area-badge`, this rendered as `H HEALTH` — but the Bug 1 SVG parsing corruption was also disrupting the surrounding HTML, causing the label portion to be swallowed. The letter stub was the only thing reliably surviving the broken parse.

**Fix:** Removed `area.icon` from the badge entirely. The badge now displays only the label, which is already uppercased by CSS:

```js
<span class="goal-area-badge">${area.label}</span>
```

The `icon` field remains in `AREA_META` (as single letters) but is no longer used in the card — it was only ever a migration artifact with no active rendering role.

---

### Bug 3 — Goal card body appears mostly empty with title/meta pushed to the bottom

**Symptom:** A large blank area filled the top portion of each goal card body; the title and progress bar appeared crammed at the bottom.

**Root cause:** This was a direct consequence of Bug 1. The malformed SVG strings produced by the `&quot;` entity problem were injected as text nodes into the DOM. The browser interpreted the broken SVG attribute strings as visible text content inside the `goal-card-body`, creating invisible-but-layout-consuming text nodes that pushed all real content downward. Because the text had no explicit height or color, it appeared as whitespace — but it occupied vertical space in the flex column.

**Fix:** Resolved automatically by fixing Bug 1. With properly interpolated SVG constants, the card body contains only the expected elements: header, title, meta row, progress bar, milestones, and habit chips.

---

## ✅ Changes in v1.3.7

```
v1.3.7 — Goal Card Bug Fixes
fix: goal card action buttons now render correctly (trophy, edit, delete)
fix: area badge displays full label ("Health", "Relationships") not stub letter
fix: goal card body layout correct — no blank area above title
refactor: extracted GOAL_SVG constant object for safe SVG interpolation in template literals
refactor: removed area.icon from goal-area-badge template (single-letter stubs were never valid display values)
refactor: removed lingering emoji (📍 🔗) from goal-card-meta milestone/habit count strings
```

---

## 🧪 Test Checklist (3 min)

**Goal card fixes:**

- [ ] Create a goal → card appears with trophy, edit (pen), delete (trash) buttons top-right
- [ ] Achieved goal → trophy button hidden, "Goal Achieved" banner visible
- [ ] Badge shows full word: "HEALTH", "RELATIONSHIPS", "CREATIVE" (not "H", "R", "Cr")
- [ ] Card body fills correctly — title appears near top, no blank space above it
- [ ] Click trash → confirm modal → goal deleted
- [ ] Click pen → goal modal opens pre-filled with correct data
- [ ] Click trophy → goal marked achieved, banner appears

**Regression:**

- [ ] Loading screen appears and fades correctly
- [ ] All 7 nav icons still render (SVG system from v1.3.6 intact)
- [ ] Area picker icons in goal form still show (SVG in static HTML, unaffected)
- [ ] Analytics, Habits, Journal, Calendar pages unaffected
- [ ] Light/dark toggle and all 10 themes work
- [ ] Goals persist across page refresh (localStorage key `ascend-goals-v1.3.4`)

---

## 📝 Changelog

```
v1.3.7 — 2026-02 — Goal Card Bug Fixes (patch)
v1.3.6 — 2026-02 — Rocket Loading Screen + SVG Icon System
v1.3.5 — 2026-02 — Analytics Fix + Entrance Animations
v1.3.4 — 2026-02 — Goals & Milestones
v1.3   — 2026-02 — Journal
```

---

**Version:** 1.3.7  
**Type:** Bug Fix  
**Release:** February 2026  
**Mantra:** A card that renders is worth a thousand that don't.
