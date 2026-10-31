# CURRENT_STATE.md

## Status
- Chapter 1: Levels 1–4 playable. Level 5 "The Stranger" and Level 6 are locked placeholders.
- `DEV_UNLOCK_ALL = true` for testing; set false for release.
- Branch: `level4-dash-obstacles-open-road` (local, not pushed). Latest commit: `5f0976c`.

## Level 4 — latest changes (awaiting hand-play)
- Open Road shortened by 1000 px (`CHCUT`); finale items placed from `CHX`.
- Dash-only falling barriers (`fall`): thin (44 px), 196 px tall, drawn size = collision size.
  - Start falling only when Veera is about 140 px from impact, so the dash window opens right as it lands.
  - Correct dash window about 130 px; jumping never clears them.
  - Telegraph: hanging barrier, blinking footprint strip, dashed drop lines, edge arrow off-screen.
- Start camera: never pans backward; no zoom dip; car enters from the left edge.
- Market stalls: vegetables, fruits, fish and flowers each have their own goods and textures.
  - Bananas hang from an awning hook.
  - Construction zone: warning triangle replaced by a scaffold tower.
- Shake: Level 4 uses smooth wobble instead of random jitter; barrier and car-hit shake reduced.

## Verification (headless only)
- Dash sweep: correct window 126–132 px in all zones; jump never clears; no errors.
- Bot run: win with 0 mistakes. No-input run: ends after third mistake.
- Levels 1–3 load and run 600 frames with no errors.
- Start camera: 0 backward frames in a 30 s trace.
- **Not hand-played.** Vibration report not confirmed on screen.

## Open decisions
- Explosion white glow (`CH.gl`) breaks the no-white-flash rule. Grey or keep as fireball highlight?
- Level 4 pacing and gap feel (400/340/280 px) need hand-play.
- Tip persistence across reloads (not possible in artifacts).
- Goons small at chase zoom; pedestrians and fleeing crowds not implemented.
- Blinking white lamp on Level 3 barricade is intentional; keep unless asked.

## Next
1. Hand-play Level 4; report anything that feels off, with zone and position.
2. Approve or reject the Level 4 pass.
3. On approval: push branch, merge to `main`, update this file.
4. Decide open items above.
5. Then Level 5 "The Stranger".
