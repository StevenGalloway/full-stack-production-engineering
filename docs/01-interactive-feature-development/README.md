# Interactive Feature Development

## Purpose

Add a new interactive editing capability to an existing canvas-based rendering system, without breaking any record created before the feature existed. The platform renders user-authored diagrams made of positioned, connected elements; one category of element had always rendered as visually unbounded, when in reality it represents a bounded segment whose true start and end the user had no way to specify.

## Context

The rendering system was built directly on a low-level 2D canvas library (shapes, groups, and transforms — not a diagramming framework with built-in editing primitives). Every coordinate is either in world space (matching the underlying diagram) or in screen space (matching the viewport), and the two are only equivalent at a single, specific zoom/pan state. Any new interactive element has to be deliberately placed in the correct space or it silently drifts relative to everything else the moment the user zooms or pans.

## What Was Built

- Two draggable handles per element, letting the user define its actual start and end, replacing the prior implicit "renders forever in both directions" behavior
- **Opt-in state modeling**: the new fields are simply absent on every element that predates the feature, so untouched records render exactly as before — no migration, no backfill
- A proximity-based validation rule: if a required nearby element isn't within a defined distance of either handle, the element is flagged
- That validation result was wired into the system's existing pass/fail evaluation for the containing group, so a failing handle can now fail the group even when every other existing rule passes
- A centered, in-app confirmation dialog replacing a native browser `alert()` for the new failure case, listing every applicable failure reason in one place instead of one popup per condition

## Diagrams

- `diagrams/feature-state-flow.mmd` — how a user-authored change to a handle propagates into the group's pass/fail state

## Key Design Decisions

| Decision | Rationale |
|---|---|
| Feature fields absent until first use (opt-in) | Avoids a migration entirely; every pre-existing record is provably unaffected |
| Validation logic centralized in one shared utility, called from both the visual-warning path and the pass/fail path | Prevents the two from silently disagreeing about what "compliant" means |
| New failure dialog reuses the app's existing modal component rather than introducing a new one | Keeps the UI consistent and avoids adding a second modal implementation to maintain |

See `decisions/adr-0001-opt-in-state-modeling.md` and `decisions/adr-0002-shared-validation-logic.md`.

## Coordinate-Space Defect (Caught Before Shipping)

An early version of the handles was positioned in screen/viewport space to match the (incorrect) "infinite line" rendering it was replacing. That would have made saved handle positions meaningless the moment a user zoomed or panned, and incomparable to the world-space coordinates every other element on the canvas already used. This was caught in review before shipping and corrected by rendering the handles inside the same world-space transform group as every other element.

## Rotation-Dependent Coordinate Bug (Caught After Shipping, Fixed Same Day)

A second, harder defect surfaced after initial delivery: a warning indicator that rendered correctly on one orientation of the element was completely invisible on the perpendicular orientation, despite valid geometry and no console errors. Root cause, confirmed by direct inspection of the rendered node tree rather than by guessing: the perpendicular orientation's container group applied a 90-degree rotation to make its own label readable, which silently swaps which local axis maps to on-screen horizontal versus vertical for *everything else* rendered inside that group. The warning indicator's position, copied from the working orientation, was technically valid — it just placed the indicator directly underneath an opaque, already-rendered element in the rotated frame. Fixed by recalculating the offset for the rotated frame specifically, and documented inline so the next addition to that same group doesn't repeat the mistake.

## Outcome

Shipped and verified against both the zero-data case (a group with no elements yet, which must still be flagged as incomplete regardless of where handles end up) and the fully-populated case, across both orientations the rendering system supports.
