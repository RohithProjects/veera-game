# PROJECT_MAP.md — file structure & "use this for that"
## Files
| File | Purpose |
|---|---|
| `veera.html` | The whole game (HTML + Canvas + JS). Source of truth. Edit in place; never split without asking. |
| `CLAUDE.md` | Rules: read before any change. |
| `PROJECT_MAP.md` | This guide. |
| `CURRENT_STATE.md` | Progress, flags, next task. |
Search `veera.html` by name (line numbers drift).
## Use this for that
| To change… | Go to |
|---|---|
| Key bindings / input buffer | `KM`, `keydown`, `peek`, `eat` |
| A pose or state animation | `POSE` (poses), `targetFor` (state → pose + speed), `updatePose` (blend, turn, roll) |
| Attack keyframes | `KF` + `KFX` (twist/wrist); timing/damage in `startAttack` |
| Walk/run cycle | `RH`, `RK`, `locoPose` |
| Turning | `updatePose` (turn block: `fT`, `turnK`), width clamp in `drawFig` |
| How a figure is drawn (limbs, head, headband, arms) | `drawFig`, `legPts`, `armPts`, `drawFace` |
| Two-handed weapon grip | `drawFig` (`W2`, `A2b` IK on back arm) |
| Weapon slash effect | `slashFx` (armed), plain trail in `drawChar`; points pushed in `updatePose` |
| Weapon stats / models / durability | `WSPEC`, `WDUR`, `WATK`, `weaponShape`, `weaponHits` |
| Weapon pickup / heavy reject | `findPickup`, `findHeavy`, `drops`, `clearOfProps` (keeps weapons out of props) |
| Weapon swing arcs (azimuth/tilt) | `WS[weapon:swing].p`, projected in `updatePose` into `f.wa`, `f.wl`, `f.wfw` |
| Weapon body templates / phase timing | `WB` (body poses), `WT` (phase seconds), `wBuild`, `WRDY` (ready stance) |
| Kinetic chain (joint stagger) | `WLEAD` (per-joint lead), `wSeg` (phase lookup), `wAtkPose` |
| Footwork / planted feet | `wAtkPose` footwork block, `solveH`, `footX`, `footIK` (two-bone, both feet), `f.prX`/`f.pfX` |
| Swing camera elevation | `WELEV` (in `footX` line), used in `updatePose` weapon projection |
| Twist cap / torso twist | `updatePose` (`p.tw` clamp), `drawFig` (`twv`, `sd` shoulder radius) |
| Character draw safety net | `drawChar` (try/catch) → `drawChar0` (real figure draw) |
| Weapon damage / reach / stun | `mkWeapon` → `WATK` (damage, reach, knockback, stun, lunge, lead) |
| Weapon trail / layering | `wTrail` (L3 grey swoosh), `wShaft` + `wBeh` in `drawFig` (bat behind body when cocked back) |
| Cricket blade roll / stick slide | `drawFig` (`f.wl` y-scale on cricket), `f.ws` in `updatePose` (stick slide); used by hitbox and back-hand IK |
| Weapon hit/hitbox check | `weaponHits` (ACTIVE only, in front of body), `applyHit` |
| Hit impact feedback (L3) | `ring` (thin grey ring), `dust` at strike, `spark`; L3 uses grey, not white |
| Screen effects | trauma/shake, hit-stop; vignette `vigT` (no white flash) |
| Dodge / roll | `tryDodge`, `POSE.rollA/rollB`, roll rotation in `updatePose` |
| Hit / death reactions | hurt flow (`'hit'`,`'hitH'`,`'down'`), `die`, `POSE.hitR/deathK` |
| Particles, dust, afterimages | `dust`, `parts`, `ghostOf` |
| Enemy behaviour | `crowd`, `AIP`, `maxAtk`, `decideAttack`; bosses `bossChoose3` |
| A level's layout / waves | `buildLn`, `makeBGn`, `drawEnvn`, `wavesN`/`WAVESn` (L3: `WAVES3`, `waves3`) |
| Level unlocking | `DEV_UNLOCK_ALL`, `unlocked`, `PROG`, `levelConfirm`, `PLAYABLE` |
| Menus | `screen`, `enterMenu`, `menuFrame`, `renderMenu` |
| Live-play / flash debugging | Headless harness used in tests (not in repo): run `window.__veera` with rAF frozen, sample canvas luminance |
| Debug hook / test hooks | `window.__veera` (`step`, `render`, `WATK`, `startAttack`, `cam`, `add`, `get`, `startLevel`, `unlockAll`, `holdWaves`) |
