# Polish Backlog

These are intentionally non-blocking polish notes. Keep the main track/gameplay work moving first; revisit this list after the core loop feels right.

## Item System

### Placement Rhythm
- Playtest whether PAUL sits on or just before a straight where the 3s boost feels rewarding.
- Keep Mont Blanc avoidable; it should be a light hazard/route choice, not a forced penalty on the racing line.
- Check whether the three macarons encourage different lines across the lap instead of being free pickups.

### Visual Readability
- PAUL should read as a positive pickup from a distance: warm/gold halo, clear jambon-beurre silhouette.
- Mont Blanc should read as risky: purple halo or subtle warning color, without looking like a premium reward.
- Macarons should read as collectible flavors: pink = raspberry, green = pistachio, red = strawberry.

### HUD Copy
- Keep pickup toast labels short and consistent:
  - `PAUL Boost!`
  - `Sugar Crash!`
  - `Macaron 1/3`
  - `Gourmet Mode!`
- Consider whether final UI language should stay English or shift toward a more Paris-themed naming style.

### Effect Balance
- Watch for PAUL + Gourmet stacking becoming too fast.
- Watch for Mont Blanc + Gourmet still feeling like a meaningful disturbance.
- If item stacking gets unstable, cap combined item `speedMul` around a fixed maximum instead of adding more special cases.

### Respawn Feedback
- Current behavior can stay simple: collected items disappear and later return.
- Later polish options:
  - faint ghost silhouette while cooling down
  - small sparkle before respawn
  - brief pop-in burst when an item returns

### Audio
- Add lightweight audio feedback later:
  - pickup chime
  - PAUL boost cue
  - Mont Blanc dizzy/crash cue
  - macaron collection cue
  - Gourmet Mode activation cue

## Checkpoint Markers

- Replace narrow gate-like checkpoints with wide-track-friendly markers.
- Use a stronger start/finish marker, but keep intermediate checkpoints lightweight.
- Candidate visual language:
  - full-width ground strip
  - two side flags outside the racing surface
  - highlighted next checkpoint

## Track Readability

- After playtesting, verify that the wide Tuileries circuit has enough visual edge definition while drifting.
- If players miss the road boundary, add subtle extra edge cues before changing collision or handling.

