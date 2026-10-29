# CURRENT_STATE.md

## Status
- Chapter 1: Levels 1–4 playable. Level 5 "The Stranger" and Level 6 are locked placeholders. `DEV_UNLOCK_ALL=true` for testing; set false for release.
- Repo: `veera.html` has uncommitted Level 4 changes (working tree). A copy is in outputs. Not pushed.

## Latest pass: Level 4 bug fixes (awaiting user approval)
- Car framing: camera keeps the whole car, with margin, and Veera in view; zoom only as far as needed (~1.28–1.30). Gap targets changed to 400/340/280/40 (`CHG`).
- Knockdown: car hit now launches Veera, lands, one rebound, then he stays down. Car brakes behind him. Also applies if the third mistake is an obstacle fall.
- Phantom hits fixed: the motorbike crossing had collision but no drawing. It is now drawn, with a blinking warning arrow and a matching hitbox. The early falling sign and the landed sign also had hitbox mismatches; fixed.
- Removed the fake parked-bike scenery behind the shop. Real obstacles unchanged.
- Tip hidden during knockdown.
- Verification (headless only): bot run reaches win with 0 mistakes; no-input run ends after the third mistake; car never clipped in a full run; camera max move 9 px/frame; levels 1–3 load and run 900 frames with no errors; hitbox sizes checked against drawn sizes for all hazards.
- **No hand-play yet.** Not approved.

## Open decisions
- Tip persistence across reloads (artifacts cannot use localStorage).
- Explosion white glow (`CH.gl`) breaks the no-white-flash rule. Switch to grey or keep as a fireball highlight?
- Where "The First Scar" goes in the Chapter 1 list.
- Level 4 pacing and gap feel (400/340/280 px) need hand-play.
- Goons are small at chase zoom. Pedestrians and fleeing crowds not implemented. Market and squeeze sections are light.
- Level 1–3 enemy weapon animations not visually reviewed.
- Turning tightness after the twist cap (Pass 6) needs user confirmation.
- Blinking white lamp on Level 3 barricade is intentional; keep unless asked.

## Next
1. User approves or rejects the Level 4 bug-fix pass after hand-play.
2. On approval, commit and update this file and PROJECT_MAP.md.
3. User decides the open decisions above.
4. Then Level 5 "The Stranger".

## Earlier passes (summary)
- Passes 1–2: atlas-based dodge, jump, hit and death; turning fix; two-handed grip; crescent slash; levels unlocked; Level 3 with 7 waves.
- Pass 3: white full-screen flash removed; `clearOfProps`; light swings fast, heavy a wide return swing.
- Pass 4: Level 3 weapons rebuilt as phase-driven horizontal arcs; hitbox only in ACTIVE; grey trail/dust/ring.
- Pass 5: kinetic chain (`WLEAD`), footwork (`solveH`), smaller lunges; cricket blade rolls flat; stick slides through hands.
- Pass 6: two-bone foot IK (`footIK`), elevated swing camera (`WELEV=.6`), twist cap ±1.7; negative shoulder radius in `arc` fixed (real cause of L1–L3 flash frames); `drawChar` try/catch.
- Level 4 initial build: horizontal chase, gap meter, three mistakes, finale crash and explosion.
