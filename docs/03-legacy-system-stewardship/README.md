# Legacy System Stewardship

## Purpose

Take over ongoing development and support of a production application originally built by a different developer/agency, with no design documentation, no onboarding material, and no test suite beyond a handful of smoke tests. The goal was to become productive on the codebase quickly and stay that way, without the knowledge gained along the way evaporating between work sessions.

## Context

Inherited codebases lose knowledge in a specific, predictable way: the person who wrote a workaround, hit a footgun, or deferred a fix knows exactly why — and that reasoning is almost never written down anywhere the next person will see it. Six months later, the same footgun gets hit again, at the same cost, by someone (often the same person) who has since forgotten.

## What Was Built

A single, structured context file, committed to the repository itself and treated as a living document rather than a one-time onboarding doc. It tracks:

- The stack, local dev setup, and deployment process, in exact runnable-command form
- A running list of known issues and technical debt, each with enough detail (file, symptom, root cause, repro steps) to act on immediately rather than needing to be re-diagnosed
- A running list of implemented features, written at the level of *what changed and why*, not just *what shipped*
- Domain-specific routing logic that would otherwise require reading five files in the right order to reconstruct

This file is read at the start of every work session and updated at the end of any session that changes the picture it describes — a new feature shipped, a new defect diagnosed, a workaround discovered.

## Key Design Decision

See `decisions/adr-0001-living-context-documentation.md` for the reasoning behind treating this as a maintained artifact rather than a one-time README.

## Outcome

Two concrete payoffs observed directly: a rotation-dependent rendering defect (documented in the Interactive Feature Development domain) took a fraction of the time to root-cause the second time a similar symptom appeared, because the first occurrence's root cause was already written down with enough specificity to recognize the pattern. And onboarding back into the codebase after a multi-day gap between sessions consistently took minutes, not hours, because the file itself is the entry point.
