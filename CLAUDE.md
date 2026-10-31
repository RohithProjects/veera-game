# CLAUDE.md

## Workflow
- Pull the latest `main` before starting; work on a feature branch, never directly on `main`.
- Read `PROJECT_MAP.md` to find the right section before editing.
- Make only the requested change. No unrelated edits, refactors or new docs.
- Verify before calling anything done: headless run, screenshot or play-test as relevant.
- Update `CURRENT_STATE.md` after each pass. Commit locally; push only when asked.

## Game
- `veera.html` is the whole game (single file: HTML + Canvas + JS, no assets). Keep the name VEERA.
- Controls are fixed. Extra keys: P/Esc, H, M, R, Q, Enter, E.
- Levels 1–3: props are background, only walls block, no solid obstacles, Level 2 weapon-free.
- Visual style: grayscale, light fill with black outline. Red headband is the only colour. No white full-screen flashes; fades are black only.

## Level 4 — The Chase
- Auto-run. Attack, block and grab disabled. Jump (Space/W) and dash (L) stay.
- The car chases from behind; the gap to the car is the health bar. Three mistakes end the run.
- Every hazard telegraphs, then has a reaction window, then collides. Visible = collidable.
- Dash-only barriers (`fall`) are taller than the jump apex, so they can only be dashed through.
- Finale: sign falls → car crashes through barrier → explosion → win.
- Level 4 code is isolated (`buildL4`, `chPre`, `chPost`, `chCam`, `drawChase`, `chaseHud`). Other levels never call it.

## Quality
- 60 fps target. Cache backgrounds, cap particles, no per-frame allocation in hot loops.
- Never let one draw call throw: clamp arc radii and angles at source.
- Headless checks before "done": 0 JS errors; Level 4 no-input run ends after third mistake; Level 4 bot run reaches win; Levels 1–3 load and run.
