# StreakOS — Personal Habit Tracker

> A Notion-inspired habit tracking dashboard built entirely in the browser.
> No backend. No build tools. No dependencies. Just open `index.html` and go.

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-blue?style=flat-square)](https://intrex786.github.io/streakos/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![HTML](https://img.shields.io/badge/Built%20with-HTML%2FJS%2FTailwind-orange?style=flat-square)](#)

---

## Screenshot

![StreakOS Screenshot](https://raw.githubusercontent.com/Intrex786/streakos/main/preview.png)

---

## Features

| Category | What's Included |
|---|---|
| **Views** | Table, Calendar (Month + Week), Stats |
| **Habits** | Add, edit, archive, emoji, color tags, notes |
| **Tracking** | Daily check-ins with per-date completion |
| **Streaks** | Current streak, best streak, 7-day rate per habit |
| **Calendar** | Month grid with intensity heatmap, week layout |
| **Day Drawer** | Per-day habit checklist, daily notes, completion % |
| **Stats** | Summary cards, weekly heatmap, per-habit progress bars |
| **Profile** | Custom avatar — initials + color, or photo upload |
| **Theme** | Light / Dark mode, persisted in localStorage |
| **Settings** | Week start day, show weekends, archived habits, data reset |
| **Import / Export** | Full JSON backup and restore |
| **Persistence** | Everything saved to `localStorage` — works offline |
| **Accessibility** | ARIA roles, keyboard navigation, focus trapping |

---

## Live Demo

**[https://intrex786.github.io/streakos/](https://intrex786.github.io/streakos/)**

---

## Getting Started

### Option 1 — Open directly (zero setup)

```bash
# Clone the repo
git clone https://github.com/Intrex786/streakos.git

# Open in your browser
open streakos/index.html
# or on Windows:
start streakos/index.html
```

No server needed. The app runs entirely from the file.

### Option 2 — Serve locally (recommended for file imports)

```bash
# Using Python
cd streakos
python -m http.server 8080
# → open http://localhost:8080

# Using Node / npx
npx serve .
# → open http://localhost:3000
```

---

## Project Structure

```
streakos/
└── index.html      # The entire application (HTML + CSS + JS)
└── README.md       # This file
└── LICENSE         # MIT License
```

The entire app lives in a single `index.html` file (~2 300 lines), organized with clear section comments:

```
<!-- SIDEBAR -->      Collapsible left nav
<!-- HEADER -->       Title, search, export/import, theme, avatar
<!-- VIEW TABS -->    Table | Calendar | Stats switcher
<!-- TABLE VIEW -->   Database-style habit table
<!-- CALENDAR VIEW --> Month grid + week layout
<!-- STATS VIEW -->   Summary cards, heatmap, progress bars
<!-- DAY DRAWER -->   Per-day habit checklist + daily note
<!-- HABIT MODAL -->  Add / edit habit form
<!-- SETTINGS MODAL --> Preferences + archived habits
<!-- PROFILE MODAL --> Avatar customisation
<!-- CONFIRM MODAL --> Reusable confirmation dialog
<!-- JAVASCRIPT -->   Organized into:
    // STORAGE LAYER
    // APPLICATION STATE
    // DATA CALCULATORS
    // SEED DATA
    // TOAST / CONFIRM
    // RENDERERS
    // EVENT HANDLERS
    // INITIALIZATION
```

---

## Data Model

All data is stored in `localStorage` under these keys:

| Key | Type | Description |
|---|---|---|
| `ht2_habits` | `Habit[]` | All habits (including archived) |
| `ht2_checkins` | `CheckinMap` | Per-date completion + daily notes |
| `ht2_settings` | `Settings` | Theme, week start, show weekends |
| `ht2_profile` | `Profile` | Display name, avatar type/color/image |

### TypeScript-style interfaces

```ts
interface Habit {
  id: string;
  name: string;
  emoji: string;
  frequency: 'daily' | 'weekly' | 'custom';
  weeklyGoal: number;
  colorTag: 'blue' | 'purple' | 'cyan' | 'green' | 'orange' | 'indigo' | 'pink' | 'red';
  createdAt: string;     // ISO date string
  archived: boolean;
  notes: string;
}

interface CheckinMap {
  [date: string]: {      // key: 'YYYY-MM-DD'
    completed: { [habitId: string]: boolean };
    note: string;
  };
}

interface Settings {
  theme: 'light' | 'dark';
  weekStartsOn: 'mon' | 'sun';
  showWeekends: boolean;
}

interface Profile {
  name: string;
  initials: string;      // 1–3 uppercase chars
  avatarColor: string;   // hex color
  avatarType: 'initials' | 'photo';
  avatarImage: string | null;  // base64 data URL
}
```

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| `Tab` / `Shift+Tab` | Move focus between interactive elements |
| `Enter` / `Space` | Toggle checkboxes; activate buttons |
| `Escape` | Close open modal or drawer |

---

## Import / Export

### Export
Click the **Export** button in the header. A `habit-tracker-export.json` file downloads immediately containing your full `habits`, `checkins`, and `settings`.

### Import
Click the **Import** button and select a `.json` file. The app validates the schema before loading and asks for confirmation before overwriting existing data.

```json
{
  "habits": [...],
  "checkins": {...},
  "settings": {...}
}
```

---

## Streak Logic

| Metric | Calculation |
|---|---|
| **Current streak** | Consecutive days ending on *today* where the habit was completed |
| **Best streak** | Longest consecutive-day run across full history |
| **7-day rate** | `(days completed in last 7) / 7 × 100` for daily habits |
| **Status — On Track** | 7-day rate ≥ 70 % |
| **Status — At Risk** | 7-day rate between 30 % and 70 % |
| **Status — Missed** | 7-day rate < 30 % |

---

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Markup | Semantic HTML5 | Accessibility-first |
| Styling | [Tailwind CSS](https://tailwindcss.com) via CDN | Utility-first, zero build step |
| Logic | Vanilla JS (ES6+) | Zero dependencies, maximum portability |
| Icons | Inline SVG | No external requests |
| Storage | `localStorage` | Offline-capable, zero backend |

---

## Customisation

### Adding a new color tag
1. Add the hex value to the `colorTag` swatch list in the **Add Habit** modal HTML section.
2. Add the corresponding `.habit-row.c-<name>` CSS rule for the left border color.

### Changing the default habits
Edit the `seedDefaults()` function in the `SEED DATA` JS section. Each habit entry follows the `Habit` interface above.

### Extending the data model
All persistence goes through the `persist*()` helpers in the `STORAGE LAYER` section. Add a new key to `SK`, a matching field in `APP`, and call your `persist*()` function after mutations.

---

## Contributing

Pull requests are welcome.

```bash
# Fork → clone → open in browser → edit index.html → test → PR
git checkout -b feature/my-improvement
# make changes to index.html
git commit -m "feat: describe your change"
git push origin feature/my-improvement
# open a pull request on GitHub
```

Please keep changes to the single-file format and avoid introducing build tools or external dependencies.

---

## License

[MIT](LICENSE) © 2026 [Intrex786](https://github.com/Intrex786)
