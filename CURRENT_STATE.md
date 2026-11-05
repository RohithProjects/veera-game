# CURRENT_STATE.md

## How to resume in a new chat
1. Read `CLAUDE.md` and this file.
2. Read the relevant parts of `veera.html` before changing anything.
3. Level 5 hand-test is still pending. Don't mark it approved until the user says so.
4. Hosted preview of the game (artifact) does not load for the user. Deliver the game as a downloaded `veera.html` file.

## Chapter 1 status
- Playable: Levels 1–5. Level 4 "The Chase" awaits hand-play approval. Level 5 "The Stranger" is a first playable draft, headless-tested only.
- Level 6: locked placeholder.
- `DEV_UNLOCK_ALL = true` for testing (line ~2389). Set to `false` for release.
- `PLAYABLE = 5` (line ~2130).

## Repo and branches
- In this session's clone: `main` at `5a51ce7` ("Updated md files"). Clone is shallow.
- Branch `level5-the-stranger` (local only, not pushed), created from `main`:
  - `1ef26cf` Level 5 first playable draft (isolated)
  - `901fe78` Level 5 redesign: weapons, longer level, textures, face fixes
  - `f2f6cd4` Level 5 Stranger character redesign, weapon-free boss fight, smoother run bob
- Uncommitted: none at time of writing.
- Level 4 branch on the user's side: `level4-dash-obstacles-open-road`, latest `f3f634a`. Not in this session's clone. Its Level 4 fixes may not be in `main` `5a51ce7`. **Before merging anything, check that Level 4 on `level5-the-stranger` matches the newest Level 4 code.**
- Nothing pushed or merged.

## Level 5 — The Stranger (draft status)
- Flow: market lane sighting → first ambush (one armed goon with a stick) → second group, gate closes → silhouette leads Veera past shops → phone booth rings, shrine-hall ambush (three men) → rooftop silhouette and two drops → cinema door → train over viaduct → bridge ambush → floodlit lorry yard (three goons) → the Stranger duel → he withdraws at ≤45% HP → lorry → win.
- Length: 6400 px world (was 4700).
- Weapons: one stick in the lane (x≈1760). Pickup closes at the yard trigger (stage 6). The yard is weapon-free, including the Stranger.
- The Stranger: 280 HP, layered dark clothing, slicked hair with grey rim, moustache, serious face, calm idle walk, compact guard stance when fighting.
- Veera: angry face for 5 seconds after the Stranger withdraws, then calm.
- Textures added: brick courses, plaster grain, shop-sign streaks, window grills, awnings, kerb grain.

### Level 5 — verified headlessly (not by hand)
- Bot playthrough reaches the win with and without a health boost (~101–107 s). 0 JS errors.
- Weapon pickup works in lane and Level 3; Level 2 still blocks pickup.
- Weapon removed at the yard gate; nothing can be picked up after it.
- Death and restart work.
- Frame cost ~3 ms (viaduct) to ~6.3 ms (mandapam) in software-rendered headless Chromium; Level 3 ~6 ms.

### Level 5 — not hand-tested / open
- Difficulty: bot wins easily; Stranger's 280 HP and attack pace are first guesses.
- Whether the Stranger reads clearly against the dark building, road, shop sign and floodlight at normal play speed.
- Pacing of new beats (phone booth, rooftop, cinema door).
- Tamil text on new signs (phone booth "STD", cinema name, cinema poster, shrine hall, poster): not verified by a native reader.
- Hair: reads as hair or as a cap at small scale. Needs eye check.
- Stranger's walk away after withdrawal may cross Veera if he was on Veera's left.
- Sound: reused existing SFX (engine, shutter, metal, heavy, step). Check suitability.

## Movement and motion — changed this session
- Run bounce (all levels): old bounce was a sharp 7 px pulse twice per stride. Now `f.bob = r*3.2*sin²(h·π)` with light smoothing `f.bobS`. Peak per-frame bounce step dropped from ~5.3 px to ~1.1 px in headless measurement.
- Affects Levels 1–4 as well as 5 (shared locomotion). **Level 4 run feel must be checked by hand after this change.**
- Stranger's own idle and walk use `strangerPose`, gated by `f.hood`.
- User decision: existing movements are approved and should not be changed further unless asked.

## Shared-code edits this session (Levels 1–4 touched)
- Weapon gating extended from `level===3` to `level===3||level===5` for pickup, swing trail, hit ring, and `findPickup`.
- Run bounce smoothing (all levels, see above).
- Head/face code: `drawFace` gets a Level 5 and `f.hood` branch; the Stranger's head drawing is gated by `f.hood`.
- Torso: `tw` widened for `f.hood`; Stranger-only torso drawing is gated by `f.hood`.
- Hair/moustache/face drawing gated by `f.hood`.
- Reviewed as backward-compatible; Level 1–3 bot runs still play without errors. **Level 4 win was not re-run after these edits.**

## Known issues to check
- Hosted artifact preview: "Still loading" / "artifact took too long". Not solved. Deliver as a file.
- Test hook `window.__veera` is still in the game file. Remove or gate before release.
- Level 4 explosion white glow (`CH.gl`) breaks the no-white-flash rule. Keep as fireball highlight, or change to grey? Still undecided.
- Goons are small at chase zoom.
- Pedestrians and fleeing crowds not implemented in Level 4.
- Tip persistence across reloads is not possible (no storage).
- Levels 1–3 enemy weapon animations not visually reviewed.
- Blinking white lamp on the Level 3 barricade is intentional; keep unless asked.

## Next steps
1. User hand-plays Level 5 (no cheats) and reports which section felt wrong: lane, ambushes, shrine hall, rooftop, cinema, viaduct, yard, duel, exit.
2. User hand-plays Level 4 to check the run-bounce change.
3. On approval: merge Level 5 branch into the correct Level 4 base, check Level 4 again, remove the test hook, set `DEV_UNLOCK_ALL=false` only if the user asks, and update this file.
4. Then start Level 6 (locked placeholder).

## Test recipe (scripts are not in the repo)
- Playwright Chromium. Load `file://…/veera.html`.
- `window.__veera.startLevel(5,false)`, then set `window.__veera.get().player.x` and `window.__veera.S5().stage` to jump.
- Bot: drive `d` and `j`/`k`/`l` keys toward the nearest enemy; god mode by resetting `player.hp`.
- Check: JS errors via `pageerror`; per-frame body step via `bobS`; weapons via `get().player.weapon` and `get().drops`.
