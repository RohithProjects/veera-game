# CURRENT_STATE.md

## Chapter 1
- Playable: Levels 1–4. Level 4 "The Chase" is the current pass, awaiting hand-play approval.
- Locked placeholders: Level 5 "The Stranger", Level 6.
- `DEV_UNLOCK_ALL = true` for testing. Set to `false` for release.

## Repo
- Current work: local branch `level4-dash-obstacles-open-road`, latest commit `f3f634a`. Based on `main` (`41440ff`).
- All current Level 4 work is on the branch. It is not pushed or merged.

## Level 4 — confirmed (headless only)
- Bot run reaches the win with 0 mistakes.
- No-input run ends after the third mistake.
- Dash barrier: correct dash window is about 130 px in all four zones. Jump never clears it.
- Start camera: never pans backward, no zoom dip; car enters from the left edge.
- Levels 1–3 load and run without JS errors.

## Level 4 — not hand-tested
- Entire pass: pacing, gap feel (400/340/280 px), dash timing feel, camera and shake feel, market stall visuals, finale.
- A "vibrating" report was not confirmed on screen. Shake was smoothed as a precaution.

## Level 4 — open issues / decisions
- Explosion white glow (`CH.gl`) breaks the no-white-flash rule. Keep as a fireball highlight, or switch to grey?
- Goons are small at chase zoom.
- Pedestrians and fleeing crowds are not implemented. Market and squeeze sections are light.
- Tip persistence across reloads is not possible (no storage in artifacts).

## Other open items
- Levels 1–3 enemy weapon animations not visually reviewed.
- Turning tightness after the twist cap needs user confirmation.
- Blinking white lamp on the Level 3 barricade is intentional; keep unless asked.

## Next step
1. User hand-plays Level 4 and reports issues with zone and position.
2. On approval: push the branch, merge to `main`, update this file.
3. Then start Level 5 "The Stranger" (currently a locked placeholder).
