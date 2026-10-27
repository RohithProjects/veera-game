# CURRENT_STATE.md
## Chapter / level
Chapter 1: Levels 1 "A Quiet Morning", 2 "The Last Bus", 3 "The Broken Road" and 4 "The Chase" implemented. Level 5 "The Stranger" and Level 6 "The Night Everything Changed" are locked placeholders. Flow: Home → Chapter 1 → level select → play; completing n unlocks n+1. All levels are unlocked for testing (`DEV_UNLOCK_ALL=true`; set false for release).
## Level 4 — The Chase (current)
- Horizontal one-way chase. The car starts behind Veera, drives from the left, and tries to catch him. Veera runs on his own (auto-run); the player only jumps (Space/W) and dashes (L). Attack, block and grab are disabled on this level.
- Gap meter (top left) shows distance to the car. Three mistake pips track clips, stumbles and falls. Each mistake lowers the car's target gap (450 → 370 → 300 → 40). Touching the car (gap < 16) is a knockdown, which ends the run. Failing shows "KNOCKED DOWN" and restarts with R.
- Four sections (street, market, construction, open road) with rising speed (400–540 px/s) and about 50 obstacles: jumpable crates, bins, bikes, carts, barrels, pipes, debris and drains, timed motorbike crossings, and falling signs and pipes with warning markers.
- Finale: a falling sign behind Veera makes the car lose control, it hits the barrier, smoke and sparks follow, then an explosion. Veera stops and turns to the wreck, then the win screen appears.
- Shared-engine changes: `PLAYABLE=4`, level 4 branches in `startLevel`, `reset`, `simulate`, `camUpdate`, `render` and the HUD, and a speed cap override in `pLoco`. Level 1–3 code paths are otherwise unchanged.
- Tutorial tips (RUN!, JUMP, DASH) show once per page load. They are stored in memory only, so they reappear after a reload.
## Verification
- Headless bot run through the whole level: reached the win state with zero mistakes and no JS errors.
- Headless no-input run: ends cleanly with the third mistake and restarts correctly.
- Levels 1–3 still run 900 frames each with no errors (about 6–9 ms per frame in headless software GL).
- Syntax check and headless runs only. **No hand-play yet.**
## Known / open
- Tips reset on reload. Decide whether they should persist (for example in localStorage in the repo version; artifacts cannot use it).
- Level 4 explosion uses a white radial glow (`CH.gl`) at the moment of detonation. The rule says no white flashes anywhere, so this needs a decision: switch to grey or keep as a fireball highlight.
- Goons are small at chase zoom; pedestrians and fleeing crowds are not implemented; market stalls and narrow-road squeeze are lightly done.
- Level order: "The First Scar" was dropped from the Chapter 1 list when Level 4 was renamed. Decide where it goes.
- Hand-play needed for jump and dash timing, speed pacing and the gap feel (450/370/300 px).
- Enemy weapon animations (Levels 1–3) were not visually reviewed in earlier passes.
- Turning is slightly tighter after the twist cap (Pass 6); user to confirm feel.
- Blinking white lamp on Level 3 barricade is intentional (not a flash); keep unless user asks.
- Repo is not pushed; the updated `veera.html` is in outputs.
## Previous passes (summary)
- Passes 1–2: atlas-based dodge, jump, hit and death animation; turning fix; two-handed weapon grip; crescent slash; levels unlocked; Level 3 with 7 waves.
- Pass 3: removed the white full-screen flash; `clearOfProps` keeps weapons out of props; light swings are fast alternating horizontal swings; heavy is a wide return swing.
- Pass 4 (Level 3 weapon rework): phase-driven swings for stick, bat, cricket and pipe in both facings; horizontal-plane arcs projected through `WS`; hitbox only in ACTIVE; grey trail, dust and ring on hits. White flash root cause in L3 fixed.
- Pass 5: kinetic chain (`WLEAD`), footwork solver (`solveH`), smaller lunges; cricket blade rolls flat; stick slides through hands.
- Pass 6: two-bone foot IK (`footIK`), elevated swing camera (`WELEV=.6`), twist capped at ±1.7, and a negative shoulder radius that threw inside `arc` fixed (this was the real cause of the L1–L3 flash frames). `drawChar` wrapped in try/catch.
## Next
1. User hand-plays Level 4 and reports jump/dash timing, pacing and gap feel.
2. User decides: tip persistence, explosion white glow, and where "The First Scar" goes.
3. After that: Level 5 "The Stranger" (locked placeholder).
