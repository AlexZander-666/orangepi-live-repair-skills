---
name: transport-provider-truth-and-burnin-classification
description: Use when diagnosing tunnel or fallback transport behavior in a provider-neutral way, especially when you must separate provider startup failures from admission failures and burn-in degradation, preserve per-provider attempt artifacts, and report exact transport classifications.
---

# Transport Provider Truth And Burnin Classification

## Overview

This skill standardizes provider-neutral transport diagnostics when fallback providers are in play. It focuses on what was attempted, whether a provider ever truly started, whether admission passed, and whether the route later degraded during burn-in.

## When to Use

- A live run can fall back to one or more tunnel providers.
- You need to distinguish `provider_startup_failed` from `transport_burn_in_failed`.
- The system writes provider attempt artifacts and you need to keep them provider-neutral.
- A provider occasionally yields a healthy sample but later degrades under burn-in.
- The user wants concrete startup-failure reasons instead of a generic "no-url" answer.

Do not use this skill for board admission recovery, Wi-Fi retarget logic, or serial shell recovery. Use it after direct-LAN admission has already been decided and provider logic is actually relevant.

## Non-Negotiables

- Direct-LAN remains the primary operating mode. Provider logic is a recovery path.
- Do not call a provider healthy just because it emitted a URL once.
- Preserve provider-neutral attempt artifacts even when the run ultimately succeeds or fails elsewhere.
- Distinguish startup, admission, and burn-in as different phases with different classifications.
- When no URL was produced, inspect provider stdout and stderr before assigning the startup reason.

## Workflow

### 1. Capture provider-neutral attempt artifacts

For each provider, write an explicit attempt summary artifact such as:

- `<provider>-attempts.json`

Also keep a provider-neutral selected artifact such as:

- `auto-tunnel-selected.json`

The artifact model should not assume `localtunnel` is the only provider in the system.

### 2. Separate the phases

Classify provider behavior across these phases:

- startup
- admission
- burn-in

Use phase-specific reasoning:

- startup asks whether the provider produced a usable URL
- admission asks whether the workstation and board can actually use that route
- burn-in asks whether the route stays healthy after it was admitted

### 3. Extract startup failure truth

When no usable URL was produced:

- inspect stdout and stderr
- derive a concrete startup reason
- write that reason into the attempt summary

Examples of startup truth:

- permission denied
- DNS failure
- connection refused
- no URL produced

Do not collapse all of these into a single generic no-url bucket when the logs support something more precise.

### 4. Require admission before burn-in

Only run burn-in after the route has already been admitted as usable.

This prevents the system from confusing:

- a startup failure
- an admission failure
- a burn-in degradation

If admission never passed, the run has no burn-in truth yet.

### 5. Classify burn-in degradation honestly

Use `transport_burn_in_failed` when:

- the provider did produce a usable route
- admission passed
- later samples degraded during the burn-in window

The point of burn-in is to reject unstable transports that only look healthy for a moment.

### 6. Report provider attempt truth

The final report should preserve:

- which providers were attempted
- which stage each attempt reached
- whether the attempt was successful
- why the attempt failed
- any startup-failure reason

This is the transport story operators need for follow-up decisions.

## Core Classifications

- `provider_startup_failed`
- `workstation_admission`
- `board_admission`
- `transport_burn_in_failed`

Use these exactly. Do not merge them into a generic fallback failure.

## Common Mistakes

- Treating URL emission as full transport success.
- Running burn-in on a route that never passed admission.
- Naming artifacts around one provider instead of the provider role.
- Recording only the final failure and losing the earlier startup or admission truth.
- Reporting `provider_startup_failed` when the real evidence shows admission passed and burn-in later degraded.

## References

- Use [startup-reason-patterns.md](./references/startup-reason-patterns.md) for concrete startup-failure mapping.
- Use [phase-boundaries.md](./references/phase-boundaries.md) for the boundary between startup, admission, and burn-in.
