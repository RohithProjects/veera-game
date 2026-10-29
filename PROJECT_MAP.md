# PROJECT_MAP.md — where things are

## Files
| File | Purpose |
|---|---|
| `veera.html` | The whole game. Source of truth. Edit in place. Search by name (line numbers drift). |
| `CLAUDE.md` | Rules. Read first. |
| `PROJECT_MAP.md` | This navigation map. |
| `CURRENT_STATE.md` | Progress, open decisions, next steps. |

## Use this for that
| To change… | Go to |
|---|---|
| Key bindings / input buffer | `KM`, `keydown`, `peek`, `eat` |
| Pose or state animation | `POSE`, `targetFor` (state → pose + speed), `updatePose` (blend, turn, roll) |
| Attack keyframes / timing / damage | `KF` + `KFX`; `startAttack` |
| Walk/run cycle, run speed cap | `RH`, `RK`, `locoPose`; `updatePlayer` (`max` line; Level 4 uses `CH.vmax`) |
| Turning | `updatePose` (`fT`, `turnK`), width clamp in `drawFig` |
| Figure drawing (limbs, head, headband, face) | `drawFig`, `legPts`, `armPts`, `drawFace` |
| Feet, planting, footwork | `solveH`, `footX`, `footIK`, `f.prX`/`f.pfX` |
| Dodge / roll | `tryDodge`, `POSE.rollA/rollB`, roll in `updatePose` |
| Hit / death reactions | hurt states `'hit'`, `'hitH'`, `'down'`; `die`; `POSE.hitR/deathK` |
| Weapon stats, hitbox, damage | `WSPEC`, `WDUR`, `WATK`, `mkWeapon`, `weaponHits`, `applyHit` |
| Weapon swing arcs / phase timing / lead | `WS[weapon:swing].p`, `WB`, `WT`, `WRDY`, `WLEAD`, `wSeg`, `wAtkPose` |
| Weapon pickup / heavy rejection | `findPickup`, `findHeavy`, `drops`, `clearOfProps` |
| Weapon effects | `slashFx`, `wTrail`, `wShaft`/`wBeh`, `ring`, `dust`, `spark` |
| Enemy behaviour | `crowd`, `AIP`, `maxAtk`, `decideAttack`; bosses `bossChoose3` |
| Level layout / waves | `buildLn`, `makeBGn`, `drawEnvn`, `wavesN` (L3: `WAVES3`, `waves3`) |
| Particles, dust, afterimages | `dust`, `parts`, `ghostOf` |
| Screen shake / hit-stop / vignette | `trauma`, `hitStop`, `vigT` |
| **Level 4 — gap, mistakes, knockdown** | `CHG` (gap targets), `CH` (state: `mist`, `fg`, `caught`, `fin`, `tip`), `chPost` (car block and hazard loop), `chObs`, `chBike` (motorbike), `chFall` (signs) |
| Level 4 car: speed, modes, crash, explosion | `chPost` car block (`c.mode`: idle/drive/brake/swerve/crash/wreck), `drawCar` |
| Level 4 hazard layout and sizes | `buildL4` (obstacle list), `CHSZ` (sizes, match visuals) |
| Level 4 camera (whole car framed) | `chCam` (branch in `camUpdate`) |
| Level 4 scenery | `chFar`, `chMid`, `chNear`, `chPole`, `drawChase` (zone colours in `sh`) |
| Level 4 goons | `drawGoon`, `drawCar` |
| Level 4 HUD and tips | `chaseHud`, `chTip`, `CHTUT` |
| Level 4 effects | `chBoom`, `drawFxP`, `CH.fx` (cap 340) |
| Level 4 wiring into shared engine | `PLAYABLE`, `startLevel`, `reset`, `simulate`, `camUpdate`, `render`, HUD branch |
| Level unlocking | `DEV_UNLOCK_ALL`, `unlocked`, `PROG`, `levelConfirm`, `PLAYABLE` |
| Level names | Chapter 1 level-select data (L4 "The Chase", L5 "The Stranger") |
| Menus | `screen`, `enterMenu`, `menuFrame`, `renderMenu` |
| Test hooks | `window.__veera`: `step`, `render`, `cu`, `CH`, `chObs`, `CHSZ`, `WATK`, `startAttack`, `cam`, `add`, `get`, `startLevel`, `unlockAll`, `holdWaves` |
