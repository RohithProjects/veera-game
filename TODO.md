# VEERA — TODO

> Source of truth is `veera.html`. Most important first.

## 1. NEXT ACTION
**Hand-play Home → Level 1 → 2 → 3 end to end** and tune by feel:
- enemy pacing after the AI rewrite (slots 105–260 px, press timers, max attackers 1/2/3) — too passive or too pressing?
- turning (0.15 s pivot) and walk/run presentation; braking lean
- weapon swings (stick/bat/cricket/pipe): arc, lag, damage, durability (8/11/10/13), pickup time (~0.9 s)
- Level 3 foreman (pipe thrusts/swings, disarm → bare-handed), wave composition
- Level 2 traffic and bus-interior final opponent (must remain weapon-free for Veera — regression-tested)

## 2. Verify
- Real-browser console + FPS check (GPU); Tamil glyph shaping on the target device (the headless renderer mis-shaped some vowel signs).
- `localStorage` persistence in the published page.
- Gamepad/touch not supported.

## 3. Gameplay improvements
- Throw a held weapon / drop it when knocked down; enemies re-arming.
- Weapon-specific hit reactions (bat vs pipe) and a distinct "dull" impact sound as durability drops (only a crack sound exists now).
- More Level 3 environment reactions (fence collapse feedback, scaffold shake).

## 4. Next content
- Level 4 "The Stranger" (raise `PLAYABLE`, `startLevel` branch, `buildL4`, `makeBG4`, waves, decide whether weapons are enabled).
- Levels 5 and 6; chapter-complete summary.
- Per-level config object instead of `if(level===n)` chains.

## 5. Polish / cleanup
- Remove or gate the `window.__veera` debug hook (`holdWaves`, `unlockAll`, `add`, `mk`, …).
- Ambient sound loops; more varied weapon impact sounds.
