# Japan Course Schedule Visualizer

## What This Is

A web app that lets a student visualize their Japanese university course schedule. Multiple files and folders are allowed. Build it in the most efficient way possible.

## Project Structure

```
course-visualizer/
├── index.html        # Entry point
├── style.css         # (optional, can keep inline)
├── app.js            # (optional, can keep inline)
└── ...
```

Single `index.html` with inline CSS/JS is fine for small scope. Split into separate files when it aids maintainability. No backend, no npm, no build tools — open directly in a browser or via a local static server.

---

## Architecture

Three UI components:

1. **Course Selector** — left sidebar panel, fixed width ~280–300px
2. **Weekly Calendar** — main content area, two side-by-side grids (Term 1 | Term 2)
3. **ECTS Tracker** — at the top of the sidebar, always visible

No state persistence. Everything resets on page reload. No routing.

---

## Course Data (Hardcoded — Do Not Modify)

```js
const COURSES = [
  { id: 'ccc-a',   name: 'Cross-cultural Communication A',          term: '1',    day: 'Mon', periods: [3,4], credits: 2 },
  { id: 'ccc-b',   name: 'Cross-cultural Communication B',          term: '2',    day: 'Mon', periods: [3,4], credits: 2 },
  { id: 'ma-a',    name: 'Managerial Accounting A',                  term: '1',    day: 'Mon', periods: [3,4], credits: 2 },
  { id: 'ma-b',    name: 'Managerial Accounting B',                  term: '2',    day: 'Mon', periods: [3,4], credits: 2 },
  { id: 'be',      name: 'Business Economics A',                     term: '1&2',  day: 'Wed', periods: [1],   credits: 2 },
  { id: 'aa-a',    name: 'Accounting Advanced A',                    term: '1',    day: 'Wed', periods: [3,4], credits: 2 },
  { id: 'aa-b',    name: 'Accounting Advanced B',                    term: '2',    day: 'Wed', periods: [3,4], credits: 2 },
  { id: 'sgb-a',   name: 'Strategy in Global Business A',            term: '1',    day: 'Wed', periods: [3,4], credits: 2 },
  { id: 'sgb-b',   name: 'Strategy in Global Business B',            term: '2',    day: 'Wed', periods: [3,4], credits: 2 },
  { id: 'mgmt-a',  name: 'Management A',                             term: '1',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'mgmt-b',  name: 'Management B',                             term: '2',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'fs-a',    name: 'Financial Services A',                     term: '1',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'fs-b',    name: 'Financial Services B',                     term: '2',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'amgb-a',  name: 'Advanced Marketing in Global Business A',  term: '1',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'amgb-b',  name: 'Advanced Marketing in Global Business B',  term: '2',    day: 'Thu', periods: [3,4], credits: 2 },
  { id: 'stats',   name: 'Statistics for Business Studies A',        term: '1&2',  day: 'Thu', periods: [5],   credits: 2 },
  { id: 'entre',   name: 'Entrepreneurship',                         term: '1&2',  day: 'Fri', periods: [4],   credits: 2 },
  { id: 'cbs',     name: 'China Business Studies A',                 term: '1&2',  day: 'Fri', periods: [5],   credits: 2 },
];
```

Period times:
- P1: 09:00–10:30
- P2: 10:40–12:10
- P3: 13:00–14:30
- P4: 14:40–16:10
- P5: 17:00–18:30

---

## Component Specs

### Course Selector (Sidebar)

- Scrollable list of all 18 courses
- Courses that share a name family (A + B variants) are visually grouped together
- Each item shows: course name (medium, 14px) + secondary line with term/day/period info (light, 12px, muted)
- Custom-styled checkbox per item — no native browser checkbox appearance
- On check: immediately updates calendar and ECTS tracker
- Hover: subtle background tint, 100ms transition
- Card-like list style, not an HTML table

### Weekly Calendar

- Two grids side by side: **Term 1 (left)** | **Term 2 (right)**
- Each grid: columns = Mon Tue Wed Thu Fri, rows = P1–P5
- Period label column on the left of each grid: shows "P1 · 09:00", right-aligned, small monospaced
- Day header row across the top: Mon/Tue/Wed/Thu/Fri, centered, medium weight
- "TERM 1" / "TERM 2" labels above each grid: small-caps uppercase, letter-spaced
- Empty cells: slightly tinted background, recede visually
- Selected course = colored block in the correct cell(s)
  - Double-period courses (P3–4) span both rows with no gap, rounded corners 8px
  - Term 1&2 courses appear in both grids simultaneously
- All course blocks use the same unified color (`#8FA8CB`)
- Block content: shortened course name, semibold 11px uppercase
- Clicking a block shows a tooltip with the full course name
- Block animation: fade in + scale(0.95→1), 150ms ease. Fade out: 120ms
- Block shadow: `0 1px 4px rgba(0,0,0,0.12)` — no borders

### ECTS Tracker

- Lives at the top of the sidebar
- Shows: large display number for total credits selected
- Format: "**X** ECTS selected" — X is big, the rest is secondary
- Progress bar below showing fullness (cap at 30 ECTS)
- Updates instantly on checkbox toggle

### Conflict Detection

- When selecting a course whose day+period overlaps an already-selected course in the same term, block the selection and show a modal popup
- Modal explains: which existing course occupies the slot, what day/period it's on, and which term(s) are affected
- User must dismiss the modal; the new course is NOT added

---

## Design System

### Typography
```
font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", sans-serif;
```
- Course names: 500 weight, 14px
- Secondary info: 300 weight, 12px
- Calendar block labels: 600 weight, 11px, uppercase, letter-spacing: 0.05em
- ECTS number: large (40–48px), thin weight (200–300)

### Color Palette
```
Background:     #F5F5F7
Surface/Cards:  #FFFFFF
Primary text:   #1D1D1F
Secondary text: #6E6E73
Accent:         #0071E3  (checkboxes, buttons, highlights)
Grid lines:     rgba(0,0,0,0.06)
Empty cells:    #FAFAFA
Course blocks:  #8FA8CB  (unified, all courses)
```

Card style: `border: 1px solid rgba(0,0,0,0.08); border-radius: 12px;`
Card shadow: `box-shadow: 0 2px 8px rgba(0,0,0,0.08), 0 0 1px rgba(0,0,0,0.04);`

### Spacing
8px base grid. All spacing = multiples of 8: 8, 16, 24, 32, 48px.

### Interactions
- Checkbox: custom styled, fills `#0071E3` with white SVG checkmark on check
- Block appear: `opacity 0→1, transform scale(0.95→1), 150ms ease`
- Block disappear: same reversed, 120ms
- Hover on list items: `background: rgba(0,0,0,0.03), transition 100ms`
- Conflict modal: fade + scale in, dismiss on button or backdrop click

---

## Layout Structure

```
┌─────────────────────────────────────────────────────────────┐
│  [ECTS Tracker at top of sidebar]                            │
│  ┌─────────────┬──────────────────────────────────────────┐  │
│  │             │                                          │  │
│  │   Course    │   TERM 1 CALENDAR  │  TERM 2 CALENDAR   │  │
│  │   Selector  │                    │                     │  │
│  │  (280–300px)│   (remaining space, side by side)       │  │
│  │             │                                          │  │
│  └─────────────┴──────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

- Sidebar: fixed width 280–300px, ECTS tracker pinned to top
- Calendar area: flex-grows to fill remaining space
- Page background: `#F5F5F7`
- Min viable viewport: 1280px wide

---

## Constraints

- **No backend**
- **No npm / build tools**
- **No state persistence** (localStorage, cookies, etc.)
- **No drag and drop**
- **No editing of course data** — hardcoded only
- **No mobile-first requirement** — desktop (1280px+) is the target
- **No gradients on UI chrome** — flat surfaces only
- **No unnecessary icons**
