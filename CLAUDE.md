# CLAUDE.md — VEERA architecture & game-development rules
Source of truth: `veera.html` (single file: HTML + Canvas + JS, no assets, no server). Read PROJECT_MAP.md for locations, CURRENT_STATE.md for progress.

## Hard rules
- **Controls never change**; only additive keys (P/Esc, H, M, R, Q, Enter, E).
- **Props are background; only walls block.** Prop impacts need a body at speed > 330. No solid obstacles.
- **Visual language:** light fill + black outline, grayscale only; the red headband is the single colour exception. No cape, no dhoti, no halo.
- **Weapons (pickup, use, heavy-object rejection) are Level 3 only.** Level 2 stays weapon-free for Veera.
- Level 2 final opponent stays skill-based, never just more HP.
- Do not change anything unnecessarily; patch, don't rewrite.
- **No white flashes in Level 3**: hit feedback there is a subtle dark tint, grey sparks, grey/small bursts, thin grey trail, no random lamp strobe. **No full-screen white flashes anywhere** (use shake, hit-stop, particles instead). Fades are black only.
- **Veera's weapon swings are phase-driven** (`WT`/`WS`/`WB`/`wAtkPose`): READY > ANTICIPATION > ACCELERATION > ACTIVE > FOLLOW-THROUGH > RECOVERY. L1 = forehand diagonal, L2 = upward return swing, L3 = power forehand, H = heavy. Light swings stay J-mashable.
- **Swings are horizontal-plane arcs, never vertical chops.** `WS[weapon:swing].p` holds [azimuth, tilt] per phase; `updatePose` projects them to screen angle `f.wa`, length `f.wl` and foreshortening `f.wfw`. Do not add overhead or downward sword arcs.
- **Kinetic chain:** joint groups sample the swing at staggered times (`WLEAD`: legs > hips > torso > shoulders > elbows > weapon). Weapon lag scales with `a.lead` per weapon. The weapon must never lead the body.
- **Footwork:** rear foot is the pivot and stays planted; front foot strides, then plants. `solveH` keeps feet from sliding. Keep lunges small (`o.lunge*=.45`).
- **Hitbox:** only in ACTIVE, only in front of the body (`(ax-f.x)*f.face>-4`), one hit per target per swing. The hitbox follows the visible weapon length (`Ws.len+f.ws`, `f.wl`).
- **Weapon-specific rules:** cricket blade rolls flat to the target at contact (`f.wl` y-scale in `drawFig`); stick slides through the hands on strikes (`f.ws`, also affects trail and back-hand IK). Bat is drawn behind the body when cocked back (`wBeh`).
- **Test hooks** (`window.__veera`): `step`, `render`, `WATK`, `startAttack`, `cam`, `add`, `get`, `startLevel`, `unlockAll`, `holdWaves`. Use them for headless checks; keep them additive.
- **Never let one draw call abort a frame.** Canvas `arc` with a negative radius throws and drops the rest of the frame (HUD, player, vignette) → visible flash. Clamp radii and angles at source; `drawChar` is wrapped in try/catch.
- **Twist is capped** (`pose.tw` ±1.7). Larger values distort the torso.
- **Feet stay on the ground:** `footIK` (two-bone) solves both legs at stance height; don't hand-tune hip/knee for planting.
- **Weapons must never rest inside props** (`clearOfProps`); prompts appear only for real weapons, heavy objects only give the strain animation.

## Architecture
- One engine for all levels. Per level: `buildLn` props, `makeBGn` art (cached in `BGs`), `drawEnvn`, `wavesN`, `startLevel(n,keep)`. `WL/WR/WORLD/CEIL` are mutable.
- Simulation time `gt` drives gameplay; hit-stop and fades use real time. Input buffer 0.16 s, parry 0.18 s, perfect dodge 0.18 s, sub-steps ≤ 1/60 s.
- Animation = joint-angle pose system: `KEYS`/`BASE`/`POSE`/`KF`. `targetFor(f)` picks a target pose + lerp rate per state; `updatePose` blends it, handles turning, roll rotation (`rotOv`), weapon spring. Non-weapon attacks are 4-keyframe sets (`KF` + `KFX` twist/wrist). Weapon attacks use the phase system (`wAtkPose`, `WB`, `WS`, `WT`, `WLEAD`) and carry a hip-rotation key (`hp`).
- Animation is visual only: never let a pose, roll or turn change hitboxes, i-frames, timing or movement speed.
- Enemy AI: `crowd()` + `AIP`; `maxAtk()` caps attackers. New enemy types need an `AIP` entry; weapon kinds need `WSPEC/WDUR/WATK/EA/decideAttack`.

## Animation model (sprite atlas)
The atlas is a **motion reference only** — never copy its look. Map: idle, walk, run (keyframed `RH/RK`), turning (eased pivot with twist + knee dip), jump (rise stretch → apex tuck → fall → land squash), dodge/roll (duck → tuck roll → rise), hit reaction (recoil → stagger → recover), death (stagger → knee buckle → lie), unarmed combo, weapon sets, pickup, weapon break, heavy reject. New states need a `targetFor` case and a face kind.

## Quality / performance
- Smooth gameplay first: no per-frame allocation in hot loops, cache backgrounds, cap particles, cull offscreen. Target 60 fps.
- Graphics upgrades go through contact shadows, lighting, particles, trails — within grayscale.
- Test headlessly (0 JS errors, no `arc` radius exceptions, no soft-locks, one hit per swing, no hits behind Veera, weapon break works, Level 2 pickup blocked, brightness spikes absent) and note anything unplayed in CURRENT_STATE.md.
