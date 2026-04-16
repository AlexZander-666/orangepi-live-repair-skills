# Transport Phase Boundaries

## Startup

Question:

- did the provider launch and produce a usable candidate URL?

If no, the classification stays in startup.

## Admission

Question:

- can the workstation and board actually use the route?

If no, classify the admission failure on the relevant side. Do not claim burn-in truth yet.

## Burn-in

Question:

- after admission passed, does the route remain healthy over the burn-in window?

If it degrades later, classify `transport_burn_in_failed`.

## Why this separation matters

Without phase boundaries, unstable transports get mislabeled:

1. startup failures look like burn-in failures
2. admission failures look like provider no-url failures
3. momentary success gets mistaken for stable success
