# CURRENT_STATE.md
## Chapter / level
Chapter 1: Levels 1 "A Quiet Morning", 2 "The Last Bus", 3 "The Broken Road" implemented; 4–6 locked placeholders. Flow: Home → Chapter 1 → level select → play; completing n unlocks n+1.
## Last two passes (animation layer + Level 3)
- Pass 1 (atlas): dodge duck→tuck roll→rise, jump rise/apex/fall, hit recoil→stagger→recover, death knee-buckle.
- Pass 2: turning now keeps ≥80% width with a stronger torso twist + knee dip (no flat-paper squash); armed characters grip weapons with **two hands** (back arm IK to the shaft); armed swings leave a **crescent slash** (grayscale fill + bright edge); **all playable levels unlocked** via `DEV_UNLOCK_ALL=true` (set false for release); **Level 3 now has 7 waves + foreman** (was 3) with fresh bat/pipe supply drops before waves 4 and 6.
- Unchanged: look, controls, hitboxes, i-frames, AI.
## Pass 3 (bug fixes + weapon feel)
- Removed the white full-screen flash (`flashW` render) that fired on heavy hits, damage and perfect dodge.
- Weapons that rested inside crates/barrels/blocks (e.g. bat at x≈880 inside the crate) made the E prompt look like a prop pickup; `clearOfProps` now moves them clear and E near props only gives the strain animation.
- Weapon swings: light = fast alternating forehand/return horizontal swings (J-mash friendly), heavy bat/pipe = wide return swing (no more vertical chop); smoother easing so it reads as swinging.
## Not done
- Special attack from the atlas.
- All three passes are syntax-checked only: not hand-played or run headlessly. Check: swing hit timing on the return swing (hits happen early in the sweep), wave balance, fps.
## Next
Hand-play L1→L3, tune swing reach/damage, then Level 4 "The Stranger".

## Pass 4 (weapon combat rework, Level 3)
- New phase system for Veera's weapons (all 4 weapons x L1/L2/L3/H, both facings), modelled on the bat reference sheet: wide load behind the shoulder, hip/torso rotation (`tw`), weight shift (`hx`), planted front foot, flat chest-height hit zone, wrapped follow-through. Weapons differ by arc, timing and body amplitude (stick light/fast, bat baseline, cricket flat face + high finish + low-to-high heavy, pipe slow start/long follow-through/long recovery).
- Hitbox = ACTIVE phase only, only ahead of the body, swept from previous tip (no tunnelling, one hit per target per swing). Turns during attacks use a fast 0.07 s flip so the visible side matches the hitbox.
- White flash cause: L3 hit-flash redrew the figure as a white silhouette and dropped the weapon for those frames, plus big white burst stars, white slash crescents and random lamp strobing. All replaced in L3 (L1/L2 unchanged). Unused `flashW` assignments removed.
- Level 2 still weapon-free (verified headlessly). Headless Chromium run of L3 with all 4 weapons: 0 JS errors, no luminance spikes.
- Debug hook now also exposes `WATK, startAttack, cam, render`.
## Not verified
- Hand-feel/balance (damage, reach), enemy weapon animations untouched, 60 fps on real hardware (headless ran ~40 fps).

- Pass 4b: swings are now horizontal-plane arcs. `WS[...].p` = [azimuth, tilt] per phase (pose keys `wph`/`wtl`); screen angle and length are projected from them in `updatePose` (`f.wl`, `f.wfw`), so the bat foreshortens when pointing at the camera instead of chopping through vertical. Hit length and drawing both use `f.wl`.

- Pass 4c (from reference videos): wider contact arcs, lower stance/bigger lunge, smooth gradient swoosh trail (grey, L3 only) covering contact+follow-through, dust on strike, thin grey ring on weapon hits.

- Pass 5 (biomechanics): kinetic chain via per-joint time leads (WLEAD: legs > hips > torso > shoulders > elbows > weapon, scaled per weapon by a.lead), hip rotation key hp, head stabilised, footwork solver (rear foot pivot planted, front foot strides then plants; solveH), smaller lunge.

- Pass 5b: cricket blade rolls flat to target (wph-based y-scale), stick slides through hands on strike (f.ws affects draw, hitbox, trail, back-hand IK). Verified headlessly: 32 weapon/face/swing cases hit only ahead, one hit per swing, weapon breaks cleanly, Level 2 pickup blocked.
