# Swoly Bible — Claude Code Brief

## Your First Move
Read these two files before doing anything else:
1. `SWOLY_BIBLE_HANDOFF.md` — full technical breakdown of the app
2. `swoly-bible.html` — the complete current source code

---

## What I'm Asking You To Do
I've been building a workout tracker app called **Swoly Bible** in Claude.ai.
It currently runs as a single HTML file dropped onto Netlify.

I have a separate app (the **hat app**) that you helped me build — it uses
**Firestore and GitHub** with a proper setup we established together.

**I want Swoly Bible migrated into that same stack and workflow.**

You know that setup. I don't want to reinvent it. Mirror whatever we did
for the hat app — same Firestore structure, same GitHub workflow, same
conventions — and bring Swoly Bible into it properly.

---

## What Swoly Bible Is
A personal workout tracker PWA. Key features:
- Push / Pull / Legs / Core workout split
- Log sets with weight + reps per exercise
- PR (personal record) auto-detection
- 7-day week strip on home screen
- Full monthly calendar — tap any past day to log or delete a session
- Backdate sessions (change date before saving)
- Edit / create / delete custom workout templates
- PR edit and delete
- Backup and restore via JSON export/import
- Discard confirmation so you can't accidentally exit a session

## Current Tech (to be replaced/upgraded)
- Preact + htm (no build step, CDN imports)
- localStorage for all data
- Single HTML file deployed by drag-and-drop to Netlify

## Data That Needs to Move to Firestore
Currently stored in localStorage under these keys:
- `wt-w` → workout templates
- `wt-s` → sessions array
- `wt-p` → personal records
- `wt-wk` → week map (legacy, can be derived from sessions)

Full data model is in `SWOLY_BIBLE_HANDOFF.md`.

---

## What I'm Handing You
- `swoly-bible.html` — full working source code
- `SWOLY_BIBLE_HANDOFF.md` — complete technical handoff doc
- The hat app codebase — so you can see our exact existing setup

## What I Need From You
1. Look at the hat app setup
2. Mirror it for Swoly Bible
3. Swap localStorage → Firestore
4. Get it into GitHub with the same workflow we use
5. Tell me exactly what to do at each step — same as we did for the hat

Take the lead. You know our infrastructure better than I do.
