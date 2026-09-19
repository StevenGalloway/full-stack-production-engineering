# Postmortem: "Shared Resource Not Visible to Invited User"

## Status
Diagnosed; root cause identified; fix recommendations delivered, awaiting client prioritization

## Symptom

A user shared a resource (invited another user by email to view/edit something they owned). The invited user reported they could not see it.

## Client's Hypothesis

The client, reasonably, suspected their tiered account system: "Could it be that a lower access tier doesn't get full sharing functionality?"

## Diagnostic Approach

Rather than accept or dismiss the tier hypothesis directly, the access path was traced end-to-end through every layer that could plausibly gate it:

1. The server-side method that grants access on invite — checked for any tier/entitlement condition
2. The database write it performs — checked whether the write itself is conditional on anything account-related
3. The reactive data-sync layer that lets an invited user's client see the newly-shared resource — checked whether its query includes any tier filter
4. The route-level access control the invited user's client evaluates before rendering the resource — checked for any tier-based gate

No tier or entitlement check existed at any of the four layers. The hypothesis was ruled out with evidence, not assumption.

## Actual Root Cause

The invite flow has two distinct outcomes depending on whether the invited email address already has an account on the platform:

- **Existing account:** access is granted immediately, unconditionally
- **No existing account:** nothing is granted yet; a second, separate confirmation step is required from the inviting user to send a "please create an account" notification instead

Both outcomes were presented to the inviting user behind a modal titled identically ("Success"), making the two outcomes visually indistinguishable in the moment. The most likely explanation for the report: the invitee never had an account at invite time, nothing was actually shared, and the inviting user's confirmation step may have been missed — compounded by the fact that the separate email-delivery outage (see the companion postmortem in this domain) could have also blocked the follow-up notification even when the confirmation step *was* completed.

## Resolution / Recommendations Delivered

- Confirmed and communicated to the client, in plain terms: sharing behaves identically regardless of account tier
- Identified the actual mechanism and gave the client a concrete diagnostic question to ask the plan owner (did the invite show a simple confirmation, or a second dialog requiring an explicit "Confirm" click?) to disambiguate this case from a simple user-error case
- Flagged the underlying UX defect — two outcomes sharing one modal title — as a separate, scoped fix, not yet implemented pending client sign-off

## Lesson

A hypothesis that names a real, non-trivial system (an entitlement model) deserves the same evidence-based treatment as a "boring" one. Ruling it out concretely, layer by layer, was the only way to hand the client an answer they could actually trust rather than a guess that happened to sound authoritative.
