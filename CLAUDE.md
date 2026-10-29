# CLAUDE.md — VEERA rules

## Workflow (mandatory)
- Sync/check the GitHub repo first and work from the latest project state.
- Read and follow this file before starting any task.
- Use PROJECT_MAP.md to find the exact file/system/section before opening or editing project files.
- Use tokens efficiently: read only what's needed; no unnecessary explanations or repeated information.
- Follow the user's current command exactly. No unrelated changes.
- Do not change existing movement, controls, levels, mechanics or working systems unless the current command requires it.
- Every requested feature/fix must be previewed and play-tested before it counts as complete.
- Keep the game name VEERA.
- Quality is mandatory: high graphics, smooth gameplay, believable human movement, performance, bug-free code. Use whatever technology/technique is needed.

## Game rules
- `veera.html` is the whole game (single file, HTML + Canvas + JS, no assets).
- **Controls stay fixed.** Only additive keys: P/Esc, H, M, R, Q, Enter, E. Level 4 is the exception: auto-run, attack/block/grab disabled; jump (Space/W) and dash (L) stay.
- **Props are background; only walls block.** Prop impacts need a body at speed > 330. No solid obstacles in Levels 1–3.
- **Visual language:** grayscale, light fill + black outline. Red headband is the only colour exception. No cape, dhoti or halo. No full-screen white flashes anywhere; fades are black only. Level 3 hit feedback uses dark tint, grey sparks and small grey bursts.
- **Weapons** (pickup, use, heavy rejection) are Level 3 only. Level 2 stays weapon-free for Veera. Level 4 has no weapons.
- Level 2 final opponent stays skill-based, never just more HP.
- Swings are horizontal-plane arcs. Never add overhead or downward sword arcs.
- Weapon lead: the weapon must never lead the body. Keep lunges small.
- Hitboxes: only in ACTIVE phase, only in front of the body, one hit per target per swing; they follow the visible weapon length.
- Animation is visual only: never let pose, roll or turn change hitboxes, i-frames, timing or movement speed.
- Twist is capped (`pose.tw` ±1.7). Feet stay planted via `footIK`.
- Weapons must never rest inside props (`clearOfProps`).

## Level 4 — The Chase
- The car always chases from behind (left). It never hits Veera from behind except as the knockdown.
- Gap = Veera.x − car.x − 134. The gap is the health bar. Targets per mistake: 400 → 340 → 280 → 40 (`CHG`). Gap < 16 is a knockdown and ends the run.
- Three mistakes per run. Each obstacle hit = one mistake. Mistakes never show as HP.
- Every hazard telegraphs (warning marker or motorbike approach), has a reaction window, then collides. Visible = collidable; invisible hitboxes are bugs.
- A car hitting a hazard is not a mistake for Veera; it slows and continues.
- Final knockdown: impact → launch → land → one small rebound → stay down. No getting up. Car brakes short of him.
- Finale: falling sign → car loses control → barrier crash → smoke/sparks → explosion → debris → settle → win.
- Tips show once per page load (`CHTUT`), short, only at first use of a control. Hidden during knockdown.
- Effects use `CH.fx` (cap 340). No per-frame allocation.

## Architecture
- One engine for all levels: `buildLn`, `makeBGn` (cached in `BGs`), `drawEnvn`, `wavesN`, `startLevel(n,keep)`.
- Level 4 is isolated (`buildL4`, `chPre`, `chPost`, `chCam`, `drawChase`, `drawChaseTop`, `chaseHud`). Other levels never call it. Shared-engine hooks are `level===4` branches only.
- Simulation time `gt` drives gameplay; hit-stop and fades use real time. Input buffer 0.16 s, parry 0.18 s, dodge 0.18 s, sub-steps ≤ 1/60 s.
- Poses: `KEYS`/`BASE`/`POSE`/`KF`; `targetFor` picks target + lerp, `updatePose` blends. Weapon attacks use phases (READY > ANTICIPATION > ACCELERATION > ACTIVE > FOLLOW-THROUGH > RECOVERY).
- New enemy types need an `AIP` entry; new weapons need `WSPEC/WDUR/WATK/EA/decideAttack`.

## Quality
- 60 fps target. Cache backgrounds, cap particles, cull offscreen, no per-frame allocation in hot loops.
- Never let one draw call abort a frame: clamp `arc` radii and angles at source; `drawChar` is in try/catch.
- Headless test before calling anything done: 0 JS errors, no arc exceptions, no soft-locks, no hits behind Veera, weapon break works, Level 2 pickup blocked, no brightness spikes, Level 4 no-input run ends after third mistake, Level 4 bot run reaches win. Note anything unplayed in CURRENT_STATE.md.
