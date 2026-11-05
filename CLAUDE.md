# CLAUDE.md — VEERA project rules

## Start every task
- Read this file, then `CURRENT_STATE.md`.
- Inspect `veera.html` only for the systems the task touches. Verify every implementation detail in the code; don't assume.
- Work only from the latest files in the repo. Do not rely on older chat summaries.

## Game-wide rules
- `veera.html` is the single game source (HTML + Canvas + JS, no assets, no npm). Keep the name VEERA.
- Make only the requested change. Preserve working systems and other levels.
- Controls are fixed (see `KM`). Don't remap or add controls without asking.
- Visual style: grayscale, light fill with black outline. Red headband is the only colour. No full-screen white flashes; fades are black only.
- Gameplay runs on simulation time `gt`; hit-stop and fades use real time.
- Smooth, believable motion. No jitter, snapping or unintended camera movement.
- No browser storage (`localStorage`, `sessionStorage`, `IndexedDB`) in the game file.

## Movement is approved — do not churn it
- Veera's locomotion is approved as smooth as of the latest pass: walk, run, stride, arm and leg swing, lean, turn, jump, land, dodge, block, attack and hit reactions.
- Do not change run/walk timing, stride (`strideK`), pose targets (`locoPose`, `POSE`, `KF`), turn easing (`updatePose`) or attack timing unless the user asks for that exact movement.
- New work adds to movement; it does not replace it. If a change would alter an existing movement, stop and ask first.
- Run bounce uses a smoothed copy, `f.bobS`, that eases toward the raw bounce `f.bob` in `updatePose`. Keep this; don't remove the smoothing.
- Measure any motion change before calling it done: per-frame step size and acceleration of the body, not just a screenshot. The check used so far: peak bounce step per frame must stay about 1–1.5 px while running.

## Controls and combat
- Weapon pickup and use: Levels 3 and 5 only. Level 2 stays weapon-free for Veera. Level 1 and Level 4 have no weapons.
- Hitboxes exist only where drawn and only in the active attack phase. Animation never changes hitboxes, timing or movement speed.
- Swings are horizontal-plane arcs. Never add overhead or downward sword arcs.
- New enemy types need an `AIP` entry. New weapons need `WSPEC`, `WDUR`, `WATK` and `decideAttack` entries.

## Levels 1–3
- Props are background; only walls block; no solid obstacles.
- Level 3 weapon rules: stick, bat, cricket bat and pipe; heavy-object rejection (`findHeavy`); pickup prompt only on pickable items.

## Level 4 — The Chase (must not break)
- Auto-run. Attack, block and grab are disabled. Jump and dash stay.
- The car always chases from behind. It only hits Veera as the final knockdown.
- The gap to the car is the health bar: `Veera.x − car.x − 134`. Targets per mistake are in `CHG`. Gap < 16 is a knockdown and ends the run.
- Three mistakes end the run. Each obstacle hit is one mistake. A car hitting a hazard is not a mistake.
- Every hazard telegraphs, then has a reaction window, then collides. Visible = collidable.
- Dash-only barriers (`fall`) must stay taller than Veera's jump apex (~137 px), so they can only be dashed through.
- Finale: falling sign → car loses control → barrier crash → explosion → win.
- Tips show once per page load (`CHTUT`) and hide during knockdown.
- Level 4 is isolated (`buildL4`, `chPre`, `chPost`, `chCam`, `drawChase`, `drawChaseTop`, `chaseHud`). Shared code touches it only through `level===4` branches.

## Level 5 — The Stranger (in progress; isolated)
- Isolated block lives at the end of the script: `L5`, `S5`, `newS5`, `buildL5`, `makeBG5`, `l5Pre`, `l5Post`, `l5Cam`, `drawEnv5`, `drawL5Actors`, `drawL5Top`, `l5DrawX`, `l5Group`, `l5Drop`, `drawLorry`, `drawTrain`, `drawSil`, `drawShutterRect`, `l5Lk`. Shared code touches it only through `level===5` branches.
- Level constants: `L5D=1700` (offset for the extra section). World width is `WORLD=6400`. Start wall `WR=2700`.
- Stage flow (`S5.stage`): 0 lane sighting → 1 first ambush (one armed goon with stick) → 2 second group, gate shuts → 3 silhouette leads Veera past the shops → 20 phone booth rings, silhouette at the shrine hall, three-man ambush → 21 fight the three → 22 rooftop: silhouette knocks a tile, two men drop → 23 cinema door opens and closes → 4 train crosses the viaduct → 5 bridge ambush (weapon-free) → 6 yard trigger → 7 floodlit yard, three goons (weapon-free) → 8 Stranger walks in → 9 duel → 10 withdraws at ≤45% HP while grounded → 11 lorry drives off → win.
- Weapons: one stick in the lane at x≈1760 and one armed goon in stage 1. Pickup is disabled from stage 6 (`findPickup` returns null), and Veera's weapon is set down at the yard gate when stage 6 starts. The Stranger fight and everything after it is weapon-free. Do not give enemies weapons after stage 1.
- Stranger (`variant 2`, `hood=true`, `calm=true`): 280 HP, poise 70, scale 1.13. Guard stance is `strangerPose`, not the default pose. Hair, moustache and serious face are in `drawFace` / the head code, gated by `f.hood`. He must not be whitened. Keep his dark clothing layered (jacket, belt, skirt, trousers) and keep the thin grey rim light.
- Veera's face in Level 5: `determined` by default during stage ≥10 loco; `angry` for 5 seconds after the Stranger withdraws (`S5.rage`), then `calm`.
- Expressions outside Level 5 are unchanged. Keep `faceKind` branches gated by `level===5` or `f.hood`.
- Level 5 has a black-only fade and no white flashes. Lamp, train and floodlight glow use the existing `glowSpr`, `coneSpr`, `poolSpr`.

## Architecture for new content
- Keep new systems behind their own level number and their own function names. Don't rename existing functions or variables unless necessary.
- Don't globally modify enemy AI, physics, combat, camera, collision, animation or rendering to solve one level's problem. If a shared function must change, check that Levels 1–4 are unchanged.

## Test hooks (debug only — remove or gate before release)
- `window.__veera` exposes: `get()` (game state), `startLevel(n,…)`, `cam`, `S5()`, `L5`, `l5Pre`, `l5Post`.
- Headless test recipe: Playwright Chromium, load `file://…/veera.html`, `startLevel(n)`, then drive by setting `get().player.x` and `S5().stage`, or with keyboard presses.

## Workflow
- Commit locally on a feature branch. Don't push to GitHub unless explicitly asked.
- Don't create new docs (PROJECT_MAP.md, TODO.md, etc.) unless asked.
- Don't update `CURRENT_STATE.md` as "approved" until the user has hand-tested the level. Update it when the user asks.
- Verify before calling anything done: headless run for JS errors, plus screenshot or play-test for visual changes.

## Quality
- 60 fps target. Cache backgrounds, cap particles, cull offscreen, no per-frame allocation in hot loops.
- Never let one draw call abort a frame: clamp arc radii and angles at source; keep `drawChar` in try/catch.
- Headless checks: 0 JS errors; Level 4 no-input run ends after third mistake; Level 4 bot run reaches win; Levels 1–3 load and run; Level 5 bot run reaches win.
