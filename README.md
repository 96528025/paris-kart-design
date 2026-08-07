# Paris Kart — Design Notes

A low-poly 3D kart racer set in the Tuileries, built with Three.js and two hand-written shaders (cel shading + outline).

**▶️ Play it: https://fj-paris-kart.netlify.app** — WASD / arrow keys.

<!-- TODO: add screenshots, then uncomment:
![Tuileries Drift Circuit](screenshots/start-line.png)
-->


---

## What this repo is

This is the **design and process record** for Paris Kart, not the game source (the source is private).

It contains the two working documents I wrote *while building* the game — a prioritized polish backlog and an asset-pipeline spec. They are here unedited. I'm publishing them because the interesting part of this project wasn't getting a kart to move; it was deciding what to fix first, what to leave rough, and how to let someone else contribute art without breaking the way the game feels.

---

## The game

A single-player time-trial circuit:

- **3 laps, 8 ordered checkpoints.** Checkpoints must be hit in sequence — cutting the course or driving backwards doesn't advance progress.
- **Lap timing** with current / best / last, plus a drift meter.
- **Three item types**, each with a distinct role in the racing line:

| Item | Role | Effect | Respawn |
|---|---|---|---|
| PAUL jambon-beurre | straight-line reward | 3s boost — `speedMul 1.18`, `accelMul 1.05`, gold trail | 8s |
| Angelina Mont Blanc | light hazard | 2s Sugar Crash — `speedMul 0.78`, `accelMul 0.70`, screen wobble | 10s |
| Macarons (×3 flavors) | route collectible | collect all three → 5s Gourmet Mode, `speedMul 1.32` | 8s after trigger |

---

## August 2026 playability update

> **Status:** implemented and tested in a preserved local preview build. The Netlify production URL above has **not** been redeployed with this iteration yet.

This iteration focused on making the first session understandable and the race loop feel complete, without rebuilding the game:

- **A real entry flow.** Added a start screen that explains the goal before the countdown begins.
- **Player-facing controls.** The menu now calls out steering, `Space` drift / Mini Boost, and `Esc` pause / resume. `R` still works as a hidden rescue/debug key but is no longer presented as a core control.
- **A complete race loop.** Added explicit menu → countdown → racing → results states, a finish summary for total / best / last lap, and a reliable “race again” path that resets race, item, effect, and vehicle state.
- **A clearer first driving view.** Raised the oversized start/finish banner so it keeps its landmark role without blocking as much of the track ahead.
- **Distinct reward and hazard language.** Mont Blanc keeps the purple Sugar Crash warning. Gourmet Mode now uses gold, coral, green, and cyan feedback so collecting macarons no longer looks like the same stun/slow effect.
- **True pause / resume.** Players can use `Esc` or a visible HUD button. Pausing freezes simulation time itself: vehicle motion, race timing, item respawns, timed effects, particles, and ambient animation. Switching away from the tab or app also pauses automatically.
- **Safer iteration.** The previous playable package was preserved before editing. The new build passed Vite production compilation and local browser checks with no runtime console errors.

### What playtesting changed

The most useful item-system finding was not a mechanics bug. Macarons and Mont Blanc behaved differently in code, but both could create purple full-screen feedback, so a positive reward and a hazard felt identical at a glance. The fix was to separate their visual semantics instead of changing the underlying item rules.

This pass also reinforced a product lesson: features that exist but are not explained might as well not exist for a first-time player. The start screen, complete results flow, and visible pause affordance therefore had higher priority than adding more content.
---

## Three decisions I'd call out

### 1. Visuals and logic are fully decoupled

Every visual object is a placeholder primitive that a `.glb` file can replace by being dropped into a known path. Swapping a model changes **only** what you see — it does not touch handling, collision, pickup radius, checkpoint detection, camera, or race timing.

If a model is missing or corrupt, the loader logs a warning and falls back to the primitive. The game never fails to start because an asset is broken.

The point was to make the project safe for a collaborator I didn't have yet. [`ASSET_EXPORT_GUIDE.md`](ASSET_EXPORT_GUIDE.md) is the contract that makes this work: units, coordinate system, origin placement, facing direction, size targets, a poly-count budget tied to the target device, and an explicit list of what must *not* be baked into a model (collision volumes, checkpoint logic, empties).

### 2. Items are designed for readability, not just effect

From [`POLISH_BACKLOG.md`](POLISH_BACKLOG.md):

> Mont Blanc should read as risky: purple halo or subtle warning color, **without looking like a premium reward.**

> Check whether the three macarons encourage different lines across the lap **instead of being free pickups.**

A player has under a second to decide whether to steer toward something. If a penalty reads as a reward, the item is broken regardless of how well the effect is tuned. And a collectible that doesn't change anyone's line isn't a design element — it's decoration.

### 3. When stacking got unstable, add a constraint — not another special case

> If item stacking gets unstable, cap combined item `speedMul` around a fixed maximum instead of adding more special cases.

PAUL + Gourmet Mode can compound into speeds the handling model wasn't tuned for. The fix that scales is one ceiling on combined multipliers, not a growing table of pairwise exceptions.

---

## The documents

| File | What it is |
|---|---|
| [`POLISH_BACKLOG.md`](POLISH_BACKLOG.md) | Prioritized polish list, explicitly marked non-blocking: *"Keep the main track/gameplay work moving first; revisit this list after the core loop feels right."* Covers item placement rhythm, visual readability, HUD copy, effect balance, respawn feedback, and track legibility. |
| [`ASSET_EXPORT_GUIDE.md`](ASSET_EXPORT_GUIDE.md) | Asset pipeline spec for Blender / OpenSCAD / CadQuery → GLB, with per-asset origin and orientation requirements and three verification cases (no asset / correct asset / broken asset). *Written in Chinese.* |

---

## Built with

Three.js · Vite · custom GLSL (cel shading, outline) · GLB asset pipeline · no game engine
