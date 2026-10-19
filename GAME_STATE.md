# VEERA — GAME_STATE

> Source of truth is `veera.html` (single file: HTML + Canvas + JS, no external assets). This document is only a continuation aid.
> Published preview: https://claude.ai/artifact/JkcXvcxY1WEDtWkqzLvcBa

## Current chapter / level
- **Chapter 1** has six planned levels; **1–3 are implemented**, 4–6 are locked placeholders ("COMING SOON").
  1 "A Quiet Morning" (tea shop) · 2 "The Last Bus" · 3 "The Broken Road" (roadside/industrial, late evening).
- **Flow:** boots to **HOME** → *CHAPTER 1* → **level select** (six signboards on a road; Veera runs to the selected one) → play.
  Completing level *n* unlocks *n+1* (saved in `localStorage` key `veera_c1`, in-memory fallback). Completed levels are replayable; locked/unavailable levels refuse entry (shake + message).
- In game: `ENTER` on a win screen → next level (or level select after level 3); `R` restart; `Q` → level select (only when paused / dead / won). The old `1`/`2` level-skip keys were **removed** (they bypassed locking).
- All three levels have been bot-tested only (not hand-played).

## Controls (unchanged since Level 1)
| Key | Action |
|---|---|
| A/D (arrows) | move (walk → run after ~0.4 s held) |
| W / Space | jump (variable height) |
| S | crouch |
| J | light attack; J-J-J chain, 3rd is a kick with big knockback; short lockout after the 3rd |
| K | heavy attack (slow, big stagger, punishable on whiff) |
| L | dodge (dash with i-frames); perfect dodge = dodge started ≤0.18 s before an enemy impact |
| I | block; tapped just before impact (≤0.18 s, no mashing) = parry |
| E | grab (J throw / K slam) · near a **weapon**: pick it up · near a chair: kick it · near a heavy object: rejection animation |
| Run + J | dash strike · S + J | low sweep (trips) |
| P / Esc | pause · H hide controls · M mute · R restart · Q level select (paused/dead/won) |

## Combat systems (shared by both levels)
- Hit-stop (real-time), camera kick/shake (trauma), blood particles + floor/wall stains, hit flash.
- Combo counter + score with variety multiplier (spam is penalised); feed labels (CLEAN HIT, COUNTER, PARRY, PERFECT DODGE, environment hits…).
- Perfect dodge: slow-mo (~0.4 s real, scale 0.4), afterimages, zoom punch, 1.25 s counter window (×1.5 damage).
- Parry: attacker staggers ~0.85 s, counter window 1 s. Enemy blocks, dodges, armor, poise (brute, bosses).
- Grab/throw/slam animations: pull-in → lift overhead → throw arc (or swing-down slam into prop/wall). Thrown bodies bowl other enemies.
- Environment props are **background scenery, never obstacles**. A prop reacts only when a *flying/knocked* body (speed > 330) enters it, or a slam targets it. Breakable props wreck once (tables, benches, crates, barrels, glass, bins); unbreakable props bounce/clang (counter, poles, lamp posts, signs, bus seats). Walls are the only hard bounds.
- **Enemy weapons** (stick / bat / cricket / pipe; Level 2 carries stick + pipe): disarmed by heavy hit, L3 finisher, counter hit, knockdown, parry, grab, env impact, death (bosses only on poise-break/knockdown/parry). **Dropped weapons are pickable ONLY in Level 3**; in Levels 1–2 they stay on the floor as decoration (tested: Veera cannot pick up or use them there).
- **Veera's temporary weapons — Level 3 only** (`findPickup`/`findHeavy` return null elsewhere): `E` near a resting weapon (≤72 px, preferred over a grab if nearer) → `pickup` state: **approach → reach → grab → lift → ready** (~0.9 s; the weapon *travels* from the floor to the hand; interruptible by hits; dodge-cancels before the lift). Models: wooden stick, baseball bat, cricket bat (broad blade), metal pipe — tapered, realistic proportions (≈50 px ≈ 0.9 m). Attacks reuse J/K via `WATK` (per-weapon timing, damage, knockback, hit-stop, SFX: stick fastest/lightest, bat stronger, cricket broad horizontal sweeps, pipe slowest/heaviest with knockdown heavy + sparks). **Weapon physics:** the weapon angle follows hand + wrist (`wr`) through a spring-damper (`wa`; heavier = softer spring = more lag/whip), torso twist (`tw`) drives shoulders/hips. **Hit detection is geometric** (`weaponHits`): 7 points along grip→tip plus swept previous-tip, only during the active window, once per target per swing (verified: no hit in wind-up, none behind/out of range). Durability: stick 8, bat 11, cricket 10, pipe 13; each landed hit −1 (heavy −2); ≤3 shows cracks + a crack sound + splinters; 0 breaks (two flying splinters, sparks for pipe, SFX, small hit-stop) and Veera is unarmed at once. Picking a second weapon drops the first (durability kept).
- **Heavy objects — Level 3 only** (`HEAVY` = concrete block, barrel, crate): `E` near them → `strain` state (grip → heave with shake/grunt → give up, hop back, dust, object rattles). Nothing is picked up; nothing breaks.

## Character design / animation
- Pictogram style: **light fill + black outline** (Veera white, enemies greys by type, leader darkest), tapered limbs, shoes, round heads.
- **Veera:** red knotted headband with tails and "வீரன்" in black on it (the *only* colour in the game), black wrist tape, no cape, no hair spikes.
- No dhoti/veshti on anyone. Faces (eyes + mouth) react: calm, determined (Veera with enemies near), alert (enemy just detected), angry/shout, grit (block), pain, fear (low-HP enemy), exhausted (low-HP Veera), smirk, strain, dizzy, panic, KO, defeat. Body language: slump when hurt, forward lean for aggressive types, higher guard for cautious, head-up when alert. Headband text is 4.6 px, clipped inside the band.
- Pose system: joint-angle poses lerped per state (now also torso twist `tw` and weapon wrist `wr`); attacks use 4-keyframe sets with anticipation → strike (ease-out) → follow-through → recovery plus shoulder/hip twist; keyframed Catmull-Rom run cycle blended with a speed-scaled walk (stride length matches speed, so no foot sliding); lean from acceleration/braking + skid pose; **turning** is a 0.15 s eased pivot (min 42 % width, tiny crouch/lean/head lead) while the gameplay facing flips instantly; ground decel 2300.

## Enemy behaviour
- Types: basic, aggressive, cautious (punishes whiffs, dodges), rusher (dash), brute (slow, armored, poise), Level 1 leader, Level 2 final opponent, Level 3 final opponent. Weapon kinds: stick, pipe, club (`w_*` attacks).
- **Enemy AI (rewritten, all levels):** per enemy `aiS` flow **IDLE (0.15–0.5 s detect, alert face) → APPROACH (runs when far) → POSITION (holds a personal slot 105–260 px by type, bobs ±26 px so it never stands frozen, depth-ranked so allies don't stack, flanks by jumping to the lighter side) → CLOSE (only when attack tokens are free, 3.2 s timeout) → ATTACK → REPOSITION**. Reacts to Veera running at them (back off) or fleeing (chase). Personal space < 50 px. Role from the director still biases who presses first; caps simultaneous attackers (1 / 2 / 3 by wave); cautious types strike openings; rushers dash from range. Boss variants keep their own logic.
- Weapon enemies use longer-reach chop/stab attacks (`w_*`), pipe = slow, armored, knockdown.
- **Level 1 leader (`boss`)**: poise, blocks/dodges adaptively, counters spam, patterns of 1–4 hits.
- **Level 3 final opponent "the foreman" (`boss`, `variant===3`)**: hard-hat, 360 HP, poise 75, **armed with an iron pipe**. Armed set (`f_*`): quick thrusts, delayed heavy swing (random 0.45–0.85 s wind-up), feint that cancels into a thrust/swing, low sweep, shoulder charge (unblockable), keeps 150–225 px, blocks/dodges/counters like variant 2. A parry/poise-break/knockdown **disarms** him → he switches to the bare-handed `c_*` set; his pipe can then be picked up.
- **Level 2 final opponent (`boss`, `variant===2`)**: cap-wearing, 340 HP, poise 70; feints (heavy wind-up that cancels), delayed second hits (random 0.65–2× timing), low sweep (jump/dodge it), dash punish on whiffs, dodge-back + `c_dash` punish, blocks ~50%+ and counters, grab only when staggered.

## Camera / physics
- Fixed side view, world→screen zoom `BASEZ=1.5`, `CAMY=470`; smooth follow with enemy look-ahead; punch zoom; trauma shake. Frame time smoothed (4-frame average); simulation sub-steps ≤1/60 s.
- Single ground plane (`GY=620`), gravity 2100, jump v 760. Characters push apart by half-width (`hw`); props are non-blocking; walls `WL/WR` are `let` (change per level/area); `CEIL` clamps head height (bus interior).

## Level facts
- **Level 1** world 2640 wide (WL 100, WR 2520). Waves: [basic×2] → [aggr, caut] → [basic, aggr, rusher] → [brute, caut, rusher] → leader. Player HP 220.
- **Level 2** world 2900 wide (WL 100, WR 2800). Props: shop wall + crates, bins, two electric poles, three street lamps, shelter (bench + breakable glass), stop sign. Waves (alternating sides, weapons): [basic+stick, basic] → [aggr, caut, basic+pipe] → [rusher, caut+stick, aggr+stick, brute+pipe]. Traffic starts after wave 2.
- **Traffic**: road zone x 900–1500; one vehicle at a time (scooter/auto/lorry), 2.1 s warning (flashing road, hazard signs, headlight glow, horn, screen-edge arrow), then crosses in <2 s. Hits: 12/18/26 dmg + knockdown (player, enemies; jump clears scooter/auto only). Enemies sometimes avoid the road.
- **Bus**: arrives after wave 3 is cleared (background lane, no collision), door opens, bouncing arrow above door. Walk into the door → fade → **bus interior** (world 1140, WL 130, WR 1010, ceiling clamp, 6 seats, 3 poles, windshield/door = window slams, swaying straps). Inside: [basic+stick, aggr+pipe] → final opponent → LEVEL 2 CLEARED.

- **Level 3** world 3200 wide (WL 100, WR 3100), dusk grayscale: dark cracked asphalt with potholes, compound wall with Tamil signs (சாலை பணி நடைபெறுகிறது / எச்சரிக்கை / வேகம் குறைவு / கட்டுமான பணி), factory skyline with animated smoke, half-built concrete frame, 3 lamps, 2 poles. Props: fences (breakable), barricades (breakable, blinking lamp), crates/barrels (breakable, heavy), concrete blocks (unbreakable, heavy), wrecked truck (unbreakable), scaffold (unbreakable). 5 weapon pickups placed (stick, bat, pipe, cricket, stick). Waves: [basic×2] → [basic+stick, caut+bat, aggr] → [brute+pipe, rusher, basic+cricket, caut+stick, aggr] → foreman. Max 3 simultaneous attackers from wave 3.

## Important implementation decisions
- Reuse the Level 1 engine. Per level: `buildLn` props, `makeBGn` art (cached in `BGs`), `drawEnvn`, `wavesN`, plus `startLevel(n,keep)`. Globals: `screen` ('home'|'levels'|'game'), `level`, `area` ('out'|'bus'), `trans` (fade, simulation frozen), `PROG.done`, `LEVELS`, `PLAYABLE`.
- Menus are drawn over the Level 1 tea-shop scene with the same camera/zoom (`menuFrame`/`renderMenu`); the menu Veera is a real fighter object driven by `updatePose` (runs between signboards, jabs on selection).
- Weapon code: `WSPEC`/`WDUR`/`WATK`, `startAttack` swaps in the weapon version of an attack, `handPoint` gives the grip position, `drops` holds every ground weapon (`rest`, `carry` flags).
- Leader variant implemented as a flag (`variant===2`) on the existing boss type, not a new type.
- Debug hook `window.__veera` is still exposed (used by bot tests).

## Current bugs / limitations
- Levels 2–3, the menus, weapons, heavy-object rejection and the new AI/turning/run have **not been hand-played** (bot tests + rendered frames only); balance numbers (weapon damage/durability, foreman, wave pacing) are untuned. Frame rate was measured only with a CPU rasteriser (≈14–16 ms/frame, ~10% better than before), not on a real GPU.
- Controls panel can overlap top-right scenery (hide with H).
- Tamil text relies on system fonts (Latha/Nirmala/Noto Sans Tamil). Levels 4–6 are placeholders. Weapons are not thrown and Veera does not drop his weapon when knocked down.
- Keyboard only; no touch controls. No save/progress beyond carried score.

## Completed
Level 1 and 2 (see above); **home screen, Chapter 1 level select, unlock/replay progression, Level 3 "The Broken Road", player weapon pickup/durability/breaking, heavy-object rejection, club weapon, Level 3 final opponent.**

## Currently working on
Nothing in progress — the fixes/Level 3 polish pass was implemented, tested and published.

## Exact next task
Hand-play Home → Level 1 → 2 → 3 end to end; check the new enemy pacing (are enemies pressing often enough / too often?), turn and run feel, weapon swing feel and durability numbers, then tune. After that design Level 4 "The Stranger".
