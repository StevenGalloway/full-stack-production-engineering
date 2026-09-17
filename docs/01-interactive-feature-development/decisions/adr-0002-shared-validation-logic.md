# ADR-0002: Single Shared Implementation for a Rule Checked in Two Places

## Status
Accepted

## Context

The new proximity rule needed to be checked in two different UI surfaces: as a per-handle visual warning at the moment a user drags a handle, and as an input into the containing group's overall pass/fail evaluation, computed separately whenever the group's data changes. These two call sites live in different layers of the codebase (a view-layer component versus a business-logic utility module) and are not guaranteed to execute at the same time or in the same order.

## Decision

Implement the proximity check exactly once, as a pure function in the shared business-logic layer, taking only the coordinate and the set of nearby elements as input. Both call sites — the visual warning and the pass/fail evaluator — call this same function rather than each re-implementing the distance comparison.

## Consequences

### Positive
- The visual warning and the pass/fail state can never disagree about whether a given handle is compliant, because they are asking the same function the same question
- A future change to the compliance distance only needs to happen in one place
- The logic became independently testable outside of any rendering context

### Negative
- The shared function has to be written generically enough to serve a UI-driven caller and a headless-evaluation caller, which took slightly longer than writing two purpose-specific versions would have

## Alternatives Considered

**Duplicate the check in both places:** Faster to write initially, but the two implementations would inevitably drift the first time either one needed a small adjustment — exactly the kind of silent, hard-to-detect defect that later erodes trust in the pass/fail indicator.
