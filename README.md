# 🎯 ASCEND v1.3.4 - Goals & Milestones

## Overview

v1.3.4 introduces the Goals & Milestones page — the first step toward making ASCEND a place where you can ascend _any_ part of your life, not just track daily habits.

**Philosophy:** Habits are the _how_. Goals are the _why_. Every habit you check off now feeds into something bigger.

---

## ✨ New Feature: Goals & Milestones Page

### What You Can Do

**Create a goal:**

- Give it a title ("Run a 5K", "Read 12 books", "Save $500")
- Pick a life area (Health, Mind, Finance, Career, Relationships, Creative)
- Optional: set a target date
- Add up to 5 milestone checkpoints
- Link existing habits — they auto-track progress

**Track it:**

- Progress bar fills automatically as linked habits complete
- Check off milestones manually as you hit them
- Mark a goal achieved at any time (or it auto-achieves when all milestones are done)

**Filter by:** All · Active · Achieved · any life area

---

## 📐 Architecture

### Progress Formula

Progress combines two signals, weighted by what's available:

```
If habits + milestones linked:
  progress = (habitProgress × 0.7) + (milestoneProgress × 0.3)

If habits only:
  progress = habitProgress

If milestones only:
  progress = milestoneProgress

If neither:
  progress = manualProgress (always 0 for now, editable in v1.3.5+)
```

**habitProgress** = % of days in the last 30 where all linked habits were completed  
**milestoneProgress** = % of milestones checked off

### Life Areas & Colors

| Area          | Color   | Hex       |
| ------------- | ------- | --------- |
| Health        | Emerald | `#10b981` |
| Mind          | Purple  | `#8b5cf6` |
| Finance       | Amber   | `#f59e0b` |
| Career        | Blue    | `#3b82f6` |
| Relationships | Pink    | `#ec4899` |
| Creative      | Orange  | `#f97316` |

Each area gets its own CSS custom property token (`--area-color`, `--area-bg`) so the color flows through the entire card — accent bar, badge, progress fill, milestone dots — all from one value.

### Data Structure

```js
// localStorage key: 'ascend-goals-v1.3.4'
{
  "goal_abc123": {
    id: "goal_abc123",
    title: "Run a 5K",
    area: "health",                    // health | mind | finance | career | relationships | creative
    targetDate: "2026-04-01",          // ISO date string | null
    milestones: [
      {
        id: "ms_1707123456",
        label: "Complete Week 1 training",
        completed: true,
        completedAt: "2026-02-10T14:32:00.000Z"  // | null
      }
    ],
    linkedHabits: ["habit_xyz789"],    // array of habit IDs
    manualProgress: 0,                 // 0–100, future use
    status: "active",                  // active | achieved
    createdAt: "2026-02-13T...",
    updatedAt: "2026-02-13T..."
  }
}
```

---

## 🖥️ Pages Touched

### New: Goals Page

- Stats strip: Active · Achieved · Milestones Hit · Avg Progress
- Filter tabs: All · Active · Achieved · 6 area filters
- Responsive goal card grid (auto-fill, min 300px per card)
- Each card: area badge, title, meta (days left, milestone count, habit count), progress bar, milestone checklist, linked habit chips
- Achieved banner on completed goals

### Updated: Dashboard

- Quick Actions: added "View Goals" button
- New Goals widget: shows up to 4 active goals with mini progress bars
- Widget links directly to Goals page

### Updated: Sidebar

- Goals nav item (🎯) between Journal and Analytics
- Works in both expanded and collapsed states with tooltip

---

## 🧪 Test Checklist (10 min)

**Phase 1: Navigation (1 min)**

- [ ] Goals appears in sidebar between Journal and Analytics
- [ ] 🎯 icon visible when sidebar is collapsed
- [ ] Hover collapsed icon → "Goals" tooltip appears

**Phase 2: Create a Goal (3 min)**

- [ ] Click "+ New Goal" → modal opens
- [ ] Enter a title
- [ ] Select a life area → card highlights in that area's color
- [ ] Set a target date
- [ ] Add 2–3 milestones (Enter key also works)
- [ ] Link 1 existing habit (if you have habits)
- [ ] Save → goal card appears in grid
- [ ] Toast notification appears

**Phase 3: Interact with goal (3 min)**

- [ ] Click a milestone checkbox → toggles done/undone
- [ ] Done milestones show strikethrough + completion date
- [ ] Progress bar updates when milestones change
- [ ] Click 🏆 → goal moves to Achieved with banner
- [ ] Click ✏️ → modal re-opens pre-filled

**Phase 4: Filtering (1 min)**

- [ ] "Active" tab → shows only active goals
- [ ] "Achieved" tab → shows achieved
- [ ] Area tab (e.g. "Health") → filters correctly

**Phase 5: Dashboard widget (1 min)**

- [ ] Dashboard shows "Active Goals" widget
- [ ] Active goals appear with mini progress bars
- [ ] "View all →" link navigates to Goals page

**Phase 6: Persistence (1 min)**

- [ ] Reload page → goals still there
- [ ] Check a milestone → reload → still checked

---

## 🗺️ Roadmap

```
v1.3.4  ✓ Goals & Milestones page (this release)
v1.3.5    Manual progress slider on goals with no habits/milestones
v1.3.6    Goals shown in Analytics — completion rate chart
v1.3.7    Calendar integration — goal target dates as markers
v1.5.0    Budget & Financial tracking page
```

---

## 📝 Changelog

```
v1.3.4 — Goals & Milestones
feat: Goals page with full CRUD (create, edit, delete, achieve)
feat: 6 life areas with color-coded cards (Health, Mind, Finance, Career, Relationships, Creative)
feat: Milestone checkboxes with completion timestamps
feat: Habit-linked auto-progress (70% weight) + milestone progress (30% weight)
feat: Filter tabs by status and life area
feat: Dashboard goals widget showing top 4 active goals
feat: Goals nav item between Journal and Analytics
feat: Auto-achieve when all milestones completed (milestone-only goals)
```

---

**Version:** 1.3.4  
**Type:** Feature  
**Release:** February 2026  
**Mantra:** Habits show what you do. Goals show who you're becoming.
