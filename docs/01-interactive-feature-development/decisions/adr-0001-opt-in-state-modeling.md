# ADR-0001: Opt-In State Modeling for a Retrofitted Feature

## Status
Accepted

## Context

The platform had years of existing production data for the element type being extended. The new capability (user-defined start/end handles) needed to ship without a data migration, without a backfill job, and without changing the rendered output of a single existing record on day one — the schema had no migration framework in place, and introducing one just for this feature was out of proportion to the problem.

## Decision

Model the new handle positions as optional fields that are simply absent until a user first interacts with the feature on a given element. All downstream logic — rendering, validation, pass/fail evaluation — treats "fields absent" as an explicit exemption case, not as a zero-value or default-value case.

## Consequences

### Positive
- Zero migration risk: every record written before this feature existed is provably unaffected, because the code path is only reached when the new fields are present
- The feature can be rolled back by simply not calling the code path that sets the fields — no data cleanup required
- Rollout can be fully gradual: individual users adopt the feature one element at a time, at their own pace

### Negative
- Every consumer of this element type now has to handle a three-state condition (unset / set-and-compliant / set-and-non-compliant) instead of a simpler two-state one
- The exemption case has to be re-verified any time the surrounding pass/fail logic changes, or it can silently regress into treating "unset" as either a pass or a fail by accident

## Alternatives Considered

**Default both handles to a computed value on read, for every existing record:** Would have given every legacy record a rendering change on day one with no user action, which was explicitly the behavior being avoided.

**Migration script backfilling every existing record:** Technically simpler downstream (no exemption case to carry forward), but this codebase has no migration framework and no staging environment to validate a migration against production-shaped data before running it against production itself. The risk of a bad backfill outweighed the complexity of carrying an opt-in state indefinitely.
