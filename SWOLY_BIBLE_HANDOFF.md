# Swoly Bible — Claude Code Handoff

## What This Is
A personal workout tracker PWA called **Swoly Bible**. Single HTML file, no build step, no backend. Deployed on Netlify via drag-and-drop. Runs on iPhone as a home screen web app.

## Tech Stack
- **Preact 10.19.3** via esm.sh CDN (React-compatible, ~3kb)
- **htm 3.1.1** via esm.sh CDN (JSX-like tagged template literals — no Babel needed)
- **localStorage** for all persistence (no database, no auth)
- **Single file:** `swoly-bible.html` — everything is in one `<script type="module">` tag
- **Font:** DM Sans via Google Fonts

## Why htm Not JSX
Babel standalone was failing silently on large scripts. htm uses tagged template literals (`html\`<div>\``) instead of JSX. Key syntax differences:
- Props: `style=${{color:"red"}}` not `style={{color:"red"}}`
- Events: `onClick=${fn}` not `onClick={fn}`
- Dynamic values: `value=${x}` not `value={x}`
- Components: `html\`<${MyComponent}/>\`` not `<MyComponent/>`
- `onInput` used for text inputs (not `onChange`)

## Data Model (localStorage keys)
```
wt-w   → workouts object   { [key]: { label, sub, color, exercises: string[] } }
wt-s   → sessions array    [{ id, date (ISO), key, label, color, exercises, sets, note, newPRs }]
wt-p   → PRs object        { [exerciseName]: { weight, reps, date } }
wt-wk  → week object       { [isoDate]: workoutKey }  ← legacy, kept for compat
```

### Session sets structure:
```js
sets: {
  "Dumbbell Row": [{ weight: 35, reps: 12 }, { weight: 35, reps: 10 }],
  "Hammer Curl":  [{ weight: 20, reps: 12 }],
  // weight: 0 means bodyweight
}
```

## Views / Navigation
Single `view` state string controls which screen renders:
- `"home"` — week strip, stats, workout cards grid, nav buttons
- `"session"` — active workout logging (sticky header, exercise cards, add-hoc exercise input)
- `"edit"` — create or edit a workout template
- `"history"` — scrollable list of past sessions with set breakdown
- `"prs"` — personal records list with edit/delete
- `"calendar"` — monthly grid, tap empty past days to log, tap filled days to view/delete
- `"backup"` — export JSON / import JSON

## Key State Variables
```js
// App-level state
view, workouts, sessions, prs, week, loading

// Active session
sKey        // workout key e.g. "pull"
sDate       // ISO date string, defaults to today, editable before save
sExList     // ordered array of exercise names for this session
sSets       // { exerciseName: [{weight, reps}] }
sNote       // string
sDone       // bool — session saved
sNewPRs     // array of exercise names that hit new PRs
sAddEx      // input value for adding ad-hoc exercise
sConfirmExit // bool — shows discard confirmation modal

// Edit workout
eKey, eData, eNewEx, eConfirm

// PRs
prEdit      // { name, weight, reps } or null

// Calendar
calMonth      // { y, m }
calPickDate   // ISO string — date awaiting workout type selection
calViewSession // session object — shown in detail modal
```

## Key Functions
```js
startSession(key, date?)  // begins session, date defaults to todayISO()
finishSession()           // saves to localStorage, detects PRs
addSet(name, set)         // adds {weight, reps} to sSets[name]
removeSet(name, idx)      // removes a set chip
addExToSession()          // adds ad-hoc exercise to sExList

openEdit(key)             // opens edit view; null key = new workout
saveEdit()                // persists workout template
deleteWorkout()           // removes from workouts, persists

deletePR(name)            // removes one PR
updatePR(name, w, r)      // edits a PR record

deleteSession(id)         // removes from sessions + syncs week strip
exportData()              // triggers JSON file download
importData(e)             // reads file input, restores all data
```

## Color Palette
```js
C = {
  bg: "#0a0a0a",       // page background
  surface: "#111",     // cards
  border: "#1e1e1e",   // subtle borders
  border2: "#252525",  // input borders
  text: "#ede9e3",     // primary text
  soft: "#555",        // secondary text / labels
  muted: "#2a2a2a",    // very dim
  green: "#b8f04a",    // accent / PRs / stats
}

COLORS = ["#4af0c8","#f04a8a","#b8f04a","#a78bfa","#f0924a","#4a9af0","#f0d44a","#e06060"]
// Used for workout card colors, picked in edit view
```

## Default Workouts
```
pull  → Back + Biceps       → #4af0c8 (teal)
push  → Chest/Shoulders/Tris → #f04a8a (pink)
legs  → Quads/Hams/Glutes   → #b8f04a (green)
core  → Abs + Mobility      → #a78bfa (purple)
```
Abs exercises are included inside each workout (not a separate block). Users can add/remove exercises via the ✏️ edit button.

## Important UX Decisions Made
1. **No back button in session** — replaced with ✕ that triggers a "Discard?" confirmation modal to prevent accidental exits
2. **Week strip reads from sessions directly** (not from `wt-wk`) so they're always in sync
3. **Per-exercise set counter** shown as a badge on each exercise card (`N sets`)
4. **Previous PR shown** under each exercise name during a session
5. **PRs auto-detected** when best weight in a session exceeds stored PR
6. **Calendar always opens on current month** — `calMonth` is reset on nav button click
7. **Backdating** — date picker in session header, max = today
8. **Session delete from calendar** — tap a colored dot → drawer shows sets → Delete button

## Requested Features Not Yet Built
- **Rest timer** — auto-countdown between sets (60/90s). Most requested.
- **Target sets/reps per exercise** — goal display (e.g. "3 × 12") shown during session
- **Body weight field** — saved BW so "BW" sets have real meaning in PRs
- **Streak protection** — nudge if no session in 2 days

## Deployment
- Hosted on **Netlify** via drag-and-drop of the single HTML file
- No build process — just edit the HTML and re-drop
- User accesses it as a home screen web app on iPhone (Safari → Share → Add to Home Screen)
- Data is per-browser localStorage — not synced across devices
- Backup/Restore feature handles phone switching

## File Structure
Everything is in one file: `swoly-bible.html`
- `<head>` — meta, Google Fonts, base CSS reset
- `<body>` — `<div id="app">` mount point
- `<script type="module">` — all JS: imports, constants, SetLogger component, App component, render call

## Component Structure
```
App (main component, all state lives here)
└── SetLogger (per-exercise set logging — weight input, reps input, + button, set chips)
```
All views are conditional returns inside App — no separate route components.
