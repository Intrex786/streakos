# StreakOS — Claude Project State

## What this project is
Single-file habit tracker web app. Everything lives in `index.html` (~3544 lines).
No build tools. No framework. Tailwind CSS + Three.js + GSAP via CDN. Vanilla JS.

## Links
- **Repo:** https://github.com/Intrex786/streakos
- **Live:** https://intrex786.github.io/streakos/
- **GitHub user:** Intrex786

## How to work on it
```bash
# Test locally — just open the file
start index.html

# After any change
git add index.html
git commit -m "feat/fix/docs: description"
git push origin main
# GitHub Pages rebuilds automatically in ~1 min
```

## App structure (nav → view mapping)
| Sidebar nav | View shown | Tab bar |
|---|---|---|
| Dashboard | #view-dashboard | hidden |
| Habits | #view-table | visible |
| Calendar | #view-calendar | visible |
| Analytics | #view-stats | visible |
| Settings | settingsModal | — |

## State object (APP)
```js
APP = {
  habits, checkins, settings, profile,   // persisted data
  currentNavView,   // 'dashboard'|'habits'|'calendar'|'analytics'
  currentTab,       // 'table'|'calendar'|'stats'
  sortCol, sortDir, calYear, calMonth,
  weekOffset, searchQuery, drawerDate, confirmCb
}
```

## localStorage keys
`ht2_habits` | `ht2_checkins` | `ht2_settings` | `ht2_profile` | `ht2_easter_egg_count`

## Key functions to know
| Function | Purpose |
|---|---|
| `displayHabits()` | Returns filtered habits (respects search + archived). Use this, NOT liveHabits(), when search matters |
| `renderDashboard()` | Re-renders entire Quantum Dashboard |
| `renderAll()` | Re-renders whichever view is active |
| `switchNav(view)` | Routes sidebar nav clicks |
| `openDrawer(dateKey)` | Opens day details drawer |
| `currentStreak(id)` | Current consecutive streak for a habit |
| `sevenDayRate(id)` | 0-100 completion rate over last 7 days |
| `animateCounter(el, n, suffix)` | Animated number count-up |
| `init3D()` | Initialises Three.js background scene |
| `pulseOrb(id)` | Pulses habit orb on checkin toggle |
| `toast(msg, type)` | Shows success/error/info toast |

## Features already built
- Quantum Dashboard (SVG ring, orbit arcs, pulse heatmap, leaderboard, feed)
- Table view (sort, inline edit, archive, status pills, streaks)
- Calendar (month + week, intensity cells, show-weekends toggle)
- Day Drawer (checklist, notes, mark all done)
- Stats (heatmap, progress bars, most consistent)
- 3D Three.js background (habit orbs, parallax camera, zone transitions, Easter egg, minimap)
- Profile modal (initials + color or photo upload)
- Import / Export JSON
- Light / Dark theme (syncs to 3D scene)
- Search (works on dashboard AND table)
- Toasts, confirm dialog, ARIA accessibility

## CSS conventions
- `.quantum-card` — glassmorphism dashboard card
- `.quantum-text` — gradient text (indigo→violet→cyan)
- `.habit-row.c-{tag}` — colored left border per habit
- `.stat-card` — hoverable stats card
- `.orbit-ring-wrap` — habit orbit ring in dashboard

## Color tag → hex
blue:#3b82f6 | purple:#8b5cf6 | cyan:#06b6d4 | green:#22c55e
orange:#f97316 | indigo:#6366f1 | pink:#ec4899 | red:#ef4444

## Bugs fixed this session
- Search bar not filtering dashboard → fixed handler to check currentNavView first; renderDashboard now uses displayHabits()
