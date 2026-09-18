# Production Incident Response

## Purpose

Diagnose customer-reported defects on a live system with no dedicated observability budget: no server-side error tracking, no staging environment, and log access limited to a hosting provider's basic console. Every incident here had to be diagnosed from whatever evidence already existed, or from evidence deliberately gathered afterward — never from a controlled repro environment.

## Approach

The common thread across both incidents below is the same: state a hypothesis, then look for the specific evidence that would confirm or kill it, before acting on it. In one case that meant walking back an initial diagnosis, out loud, to the client, once better evidence arrived. In the other, it meant tracing a symptom through every layer of the system that could plausibly cause it — data write, reactive data sync, and route-level access control — rather than stopping at the first plausible-sounding explanation.

## Incidents

- `runbooks/postmortem-third-party-delivery-outage.md` — a transactional email failure initially suspected to be a configuration defect, revised once the actual provider error was read
- `runbooks/postmortem-access-sharing-defect.md` — a "shared resource not visible to invited user" report, traced end-to-end to rule out an access-tier hypothesis and identify the real cause

## Diagrams

- `diagrams/incident-diagnosis-flow.mmd` — the general diagnostic path used for the access-sharing defect

## Observability Gaps Identified Along the Way

Neither incident was reproducible through the tools available at the time, which was itself a finding worth documenting:

| Gap | Consequence |
|---|---|
| Client-side error tracking only, nothing server-side | Server-thrown exceptions are visible only in raw hosting logs, never in the error-tracking dashboard |
| A specific upload pipeline logs failures to the browser console only | A failed upload leaves no trace anywhere a developer would normally look — not in error tracking, not in server logs |

Both gaps were written up and handed to the client as follow-up items, separate from the incidents that surfaced them.
