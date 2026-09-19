# Postmortem: Third-Party Email Delivery Failure

## Status
Resolved (external — vendor account issue, not a code defect)

## Symptom

A user-facing action that should send a transactional email (an invitation/notification flow) was failing. The client reported it simply as "email isn't sending."

## Initial Hypothesis

The server-side code reads its email-provider API key from an environment variable. A local check of that variable's value, run against the same configuration mechanism used in production, came back empty. This was reported to the client as the likely cause: the API key was probably never actually being loaded at runtime.

## Evidence That Overturned the Initial Hypothesis

The client provided the actual runtime error text from the hosting provider's log console. It was a 401 Unauthorized response from the email provider's API, with a body message stating the account's sending credits/quota had been exhausted — not a generic "invalid credentials" message.

That specific error message is only returned *after* the provider has successfully authenticated the request. An invalid or missing API key produces a different, generic 401. The evidence directly contradicted the initial hypothesis.

## Revised Diagnosis

The API key was authenticating correctly. The account itself had run out of sending quota. This is a billing/account-status issue on the vendor side, not an application defect.

## Resolution

- Client corrected on the diagnosis directly, with the specific evidence that changed it, rather than letting the earlier (incorrect) explanation stand
- Recommended action redirected from "fix the config" to "check the vendor account's plan/credit balance"

## Lesson

A locally-empty environment variable was real and worth reporting, but it was a red herring for *this* symptom — it would explain a completely different failure mode (authentication failure) than the one actually observed (a quota-exhaustion message that only a successfully-authenticated request can produce). The fix was to keep chasing evidence after the first plausible cause was found, not to stop at it.
