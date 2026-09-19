# ADR-0001: A Maintained Context File Instead of a One-Time README

## Status
Accepted

## Context

The codebase had a standard README covering setup, but nothing capturing *why* things were the way they were: deferred bugs, workarounds, dead code paths, and the reasoning behind non-obvious implementation choices. That knowledge either lived in the current developer's head or didn't exist anywhere. Development work also increasingly involved an AI coding assistant working across separate sessions with no memory between them — meaning any knowledge not written down had to be either re-discovered or re-explained, every single session.

## Decision

Maintain a single structured context file in the repository root, treated as a living document. It is read at the start of substantive work and updated at the end of any session that changes what it should say — not just when someone remembers to, but as a default habit tied to the definition of "done" for a change.

Content is organized for fast lookup over narrative completeness: a known-issues list with enough detail to act on without re-diagnosing, an implemented-features list written at the level of intent and tradeoff rather than just a changelog, and the specific cross-file routing logic that the codebase's own structure doesn't make obvious.

## Consequences

### Positive
- A defect pattern documented once is recognized faster the second time it appears, even by a different session/developer
- Re-onboarding after a gap between work sessions is fast, because the file is the entry point rather than the code itself
- Decisions made under time pressure ("we're deferring this fix, here's why") stay visible instead of silently disappearing once the immediate conversation about them ends

### Negative
- The file needs active discipline to keep current; an out-of-date context file that's trusted at face value is worse than no file, since it actively misleads rather than simply being silent
- Extra overhead at the end of every session that touches something worth recording

## Alternatives Considered

**Rely on commit messages and code comments alone:** Commit messages capture *what* changed but rarely *why*, and are expensive to reconstruct into a coherent picture later. Code comments are scattered by nature and don't capture cross-cutting concerns like deployment gotchas or known gaps in observability.

**A wiki or external documentation tool:** Separates the documentation from the code it describes, both physically (different tool, different login) and temporally (nothing forces it to be updated in the same commit as the change it documents). A file inside the repository stays versioned alongside the code and travels with it.
