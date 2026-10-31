# CLAUDE.md — VEERA project rules

## Start every task
- Read this file, then `CURRENT_STATE.md`.
- Inspect `veera.html` only for the systems the task touches. Verify every implementation detail in the code; don't assume.

## Game-wide rules
- `veera.html` is the single game source (HTML + Canvas + JS, no assets). Keep the name VEERA.
- Make only the requested change. Preserve working systems and other levels.
- Controls are fixed (see `KM`). Don't remap or add controls without asking.
- Levels 1–3: props are background; only walls block; no solid obstacles. Weapons are Level 3 only; Level 2 stays weapon-free for Veera.
- Visual style: grayscale, light fill with black outline. Red headband is the only colour. No full-screen white flashes; fades are black only.
- Gameplay runs on simulation time `gt`; hit-stop and fades use real time.
- Smooth, believable motion. No jitter, snapping or unintended camera movement.

## Level 4 — The Chase (must not break)
- Auto-run. Attack, block and grab are disabled. Jump and dash stay.
- The car always chases from behind. It only hits Veera as the final knockdown.
- The gap to the car is the health bar: `Veera.x − car.x − 134`. Targets per mistake are in `CHG`. Gap < 16 is a knockdown and ends the run.
- Three mistakes end the run. Each obstacle hit is one mistake. A car hitting a hazard is not a mistake.
- Every hazard telegraphs, then has a reaction window, then collides. Visible = collidable.
- Dash-only barriers (`fall`) must stay taller than Veera's jump apex (~137 px), so they can only be dashed through.
- Finale: falling sign → car loses control → barrier crash → explosion → win.
- Tips show once per page load (`CHTUT`) and hide during knockdown.
- Level 4 is isolated (`buildL4`, `chPre`, `chPost`, `chCam`, `drawChase`, `drawChaseTop`, `chaseHud`). Shared code touches it only through `level===4` branches. Other levels never call it.

## Architecture for new content
- New enemy types need an `AIP` entry. New weapons need `WSPEC`, `WDUR`, `WATK` and `decideAttack` entries.
- Hitboxes exist only where drawn and only in the active attack phase. Animation never changes hitboxes, timing or movement speed.
- Swings are horizontal-plane arcs. Never add overhead or downward sword arcs.

## Workflow
- Commit locally on a feature branch. Don't push to GitHub unless explicitly asked.
- Verify before calling anything done: headless run, plus screenshot or play-test for visual changes.
- Update `CURRENT_STATE.md` after each meaningful pass.

## Quality
- 60 fps target. Cache backgrounds, cap particles, cull offscreen, no per-frame allocation in hot loops.
- Never let one draw call abort a frame: clamp arc radii and angles at source; keep `drawChar` in try/catch.
- Headless checks: 0 JS errors; Level 4 no-input run ends after third mistake; Level 4 bot run reaches win; Levels 1–3 load and run.
