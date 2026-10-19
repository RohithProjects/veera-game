# VEERA — DEVELOPMENT_LOG

> Source of truth is `veera.html`. This log records what changed and why, in order.

## Timeline of important changes
1. **Level 1 prototype** — tea-shop arena, Veera + goons + leader, combat feel systems (hit-stop, perfect dodge, parry, combo, blood, procedural audio), single HTML file.
2. **Revision 1 (user feedback)** — zoom camera in (characters too small), tables/props became background scenery, cape removed, dodge rebuilt (longer dash, afterimages, wider perfect window 0.12 → 0.18 s), new moves (run-strike, low sweep), brute + rusher enemies, 4 waves, tea master + fans/bulbs ambience.
3. **Revision 2** — pause (P/Esc, auto-pause on tab hide), jog-speed tuning, proper proportions for props, redesigned figures (tapered limbs), tea-master pouring fixed.
4. **Revision 3** — removed the white halo (read as "glow"), Veera's red knotted headband with "வீரன்", keyframed run cycle with bounce and speed-matched stride, enemies run when far.
5. **Revision 4** — dhoti removed; figures recoloured to light fill + black outline; faces with reaction expressions; run cycle verified by rendering frames; zoom 1.32 → 1.5; pace normalised (slower speeds, shorter waits, milder slow-mo).
6. **Revision 5** — grab/throw/slam animation rewrite (pull-in, overhead lift, arc, swing-down slam, recovery); frame-time smoothing; turn pivot.
7. **Level 2 "The Last Bus"** — night bus stop, small shop, shelter, poles/lamps/bins, stick/pipe enemies with disarm, traffic hazard, arriving bus with door → interior combat → final opponent, level select/flow, win screen.

8. **Home / level select / progression** — home screen over the tea-shop scene, Chapter 1 signboard level select (levels 1–6 named per spec, 4–6 placeholders), unlock-on-completion with `localStorage`, replay of completed levels, locked levels refuse entry, fades between screens, `Q` back to select, win/dead overlays with next-level/replay hints. Removed the `1`/`2` skip keys.
9. **Level 3 "The Broken Road"** — new BG/props (fence, barricade, block, wrecked truck, scaffold), data-driven `WAVES3`, club weapon on enemies, 5 ground weapon pickups, final opponent variant 3 (armed foreman who is disarmable).
10. **Veera weapon mechanic** — pickup animation, weapon attack sets, durability + break, heavy-object rejection, hint bubble `E` near pickups, weapons from disarmed enemies stay pickable.

11. **Fix pass (session 4)** — enemy AI rewrite (frozen "statue" waiters), weapon system restricted to Level 3, weapon models/physics/geometry hit-boxes, bat + cricket bat added (club removed), pickup animation split into reach/grab/lift/ready, smoother eased turning, speed-scaled walk + stronger run, twist-based attack mechanics, richer expressions/body language, headband text shrunk and clipped, Tamil boards fitted (`tamilFit`) and enlarged, compact auto-hiding controls panel, contact shadows, performance pass.

## Bugs fixed (and causes)
- Enemies stood still behind tables → hop logic needed vx but collisions zeroed it; later removed entirely when props became non-blocking.
- Left-side spawns trapped in a 30 px pocket behind the counter → left spawn gated on camera position; props no longer block.
- Hop velocity lost mid-air (`hopDir` fix) before the props change.
- `AC` name clash (audio context vs accessory colour) → accessory colour renamed `ACC`.
- Bot "score 0" runs were just harness restarts after a win, not a bug.
- Perfect dodge could trigger on a zero-reach feint → `perfectCheck` now ignores attacks with no reach.
- Throw froze the victim during wind-up then teleported → victim now follows a scripted arc; thrown victims face away and land on their back (rot clamped, no spinning).
- Sprite "glow" = outline halo pass → removed.

- Pickup `ready` phase was skipped because the pickup case aborted when the weapon left `drops` → abort check now ignores the `ready` phase.
- `reset()` used to null `trans` and cleared `drops` after building props → fade transition crashed / L3 pickups vanished → `tickTrans` now nulls `trans` before calling the callback, and `drops=[]` runs *before* `buildLn`.
- Boss would be disarmed by any heavy hit through armor → boss disarm only on poise-break/knockdown/parry.
- Test-harness artifacts (not game bugs): waves spawning during unit tests (`holdWaves` hook added), enemies blocking/retreating out of range, pipe knocking a barrel into a wreck, pickup priority over rejection near a weapon.

- **Statue enemies:** waiters held a 215–320 px band and strafed at 0.3× speed (~38 px/s) while only one enemy per wave was allowed to approach, so they looked frozen. Measured before/after with a stationary player: longest near-stationary spell 2.9 s (L1) / 2.1 s (L3) → ≈1 s / 0 s. Fixed in `crowd()` (slot-based position/bob, token-gated closing, flank, reposition) — not by forced movement.
- **Weapon system was global** (pickup also worked in Levels 1–2) → gated on `level===3`; regression tests assert no pickup/strain/use in L1–L2.
- **Weapon hit-box** was an x-range test → now the real weapon segment (+ swept tip) in Level 3.
- Tamil boards clipped (Level 3 speed sign was also hidden by the concrete frame) → `tamilFit` + bigger plates, sign moved to the wall.
- Headband text too large → 4.6 px, clipped to the band.
- Cost: sub-rect background blit was slower than a full blit in the test rasteriser → reverted; offscreen culling, sprite lights, cached HUD text, in-place enemy arrays and a particle cap are in.
- Turn collapsed to a 1-px line for two frames → minimum drawn width 0.42.
- Weapon trail looked like a bent weapon → trail thinner/fainter for armed attacks.

## Decisions that must not be forgotten
- **Controls never change** (only additive: P/Esc, Q for level select, Enter on win; E also picks up weapons / strains on heavy objects).
- **Props are background; only walls block.** Prop impacts require entering at speed > 330 from outside the prop. Do not reintroduce solid obstacles.
- **Visual language:** light fill + black outline, grayscale only; the **red headband is the single colour exception**. No cape, no dhoti/veshti, no halo.
- Gameplay timing uses simulation time (`gt`); hit-stop and fades use real time. Input buffering 0.16 s, parry window 0.18 s, perfect dodge 0.18 s.
- Leader variants are flags on one boss type; weapons are fields (`weapon`, `disarmed`) on regular enemies.
- Player HP 220; zoom 1.5 is intentional (user said characters were too small, then asked for slightly bigger twice).
- Level 2 final opponent must stay skill-based (feints, delayed hits, blocks, counters), never just more HP.

- **Locking is only enforced in the menu** (`levelConfirm`); `startLevel(n)` itself does not check `unlocked()`.
- Weapon pickups are just entries in `drops` (`{k,x,y,a,rest,dur?}`); a level places them in its `buildLn` (after `drops=[]`).
- `HEAVY` set decides which props trigger the rejection animation; `UNBREAK` decides which props wreck.
- New enemy weapon kinds need entries in `WSPEC`, `WDUR`, `WATK`, `EA` (`w_*`), and `decideAttack`.
- `PLAYABLE` (=3) is the number of implemented levels; raise it and extend `startLevel`/`waves` when adding Level 4.
- **Weapon scope rule:** pickup, weapon use and heavy-object rejection are *Level 3 only*. A future level opts in by changing the `level!==3` guards in `findPickup`, `findHeavy`, `drawPickupHints` and the geometric branch in `doHits`.
- New pose keys `wr` (wrist) and `tw` (torso twist) exist in `KEYS`/`BASE`; new key-frames should set them (see `KFX`).
- Enemy AI is `crowd()` + `AIP` table; new enemy types need an `AIP` entry. `maxAtk()` is the single source for simultaneous attackers.

## Changes that can affect future levels
- `WL`, `WR`, `WORLD`, `CEIL` are now **mutable**; any new level must set them and call `startLevel` / `reset` consistently, and build its own background (`BGs[n]`) and props.
- Prop types and their break rules live in `UNBREAK` and `LBL`; new prop types need a `drawProp2` branch (or `drawSolid`) and, if breakable, a wreck drawing.
- `spawn(type, weapon, side)` and `waves2` pattern (data-driven waves) is the template for new levels; Level 1's `waves()` is still hard-coded.
- Enemy `weapon` + `w_*` attacks and `variant` boss hooks are reusable.
- `window.__veera` debug hook exists for bot testing; remove or keep behind a flag before release.
- Level transition uses `startTrans(fn)` (0.5 s fade out/in, simulation frozen).

## Testing notes (session 4)
- New suites (not part of the project, run headlessly): frozen-enemy metric (old vs new build), Level 1/2 weapon-scope regression, weapon geometry (wind-up/behind/out-of-range/one hit per swing/durability), weapon-weight lag, full flow (menus, locks, unlocks, replay), 3-level soak in god and normal mode with deaths/restarts: **0 JS errors**, no soft-locks. Frame rendered for run/turn, weapon swings, expressions, headband, all Tamil boards.
- Perf (CPU rasteriser, avg ms/frame): before 17.4 / 21.8 / 17.5 → after 14.5 / 16.2 / 15.0 (L1/L2/L3).

## Testing notes
- Latest harness (headless, stubbed canvas): menu flow (home→chapter→select, locked rejection, unlock after L1/L2/L3, replay, pause→Q), weapon pickup phases, no-teleport check, durability/break, heavy rejection (block, barrel), disarm→pickup, all three levels in god and normal mode with restarts: 0 JS errors, no soft-locks detected. Earlier harness: Level 1 and Level 2 many times: god-mode and normal, zero JS errors, waves → bus → interior → final opponent → win reached; deaths and restarts handled. Harness lived in `/tmp`, not part of the project.
- Screenshots were rendered with node-canvas to check run cycle, grab/slam, Level 2 scenes. Screenshots of home, level select, Level 3, pickup/combat/strain frames were rendered and inspected. **No manual play-through of Levels 2–3 yet; real-GPU frame rate not measured.**
