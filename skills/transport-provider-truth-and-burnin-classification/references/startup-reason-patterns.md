# Provider Startup Reason Patterns

Use provider logs to derive startup truth before reporting `provider_startup_failed`.

## Typical patterns

- `Permission denied`
  - classify as permission denied startup failure
- `Could not resolve hostname`
  - classify as DNS failure
- `Name or service not known`
  - classify as DNS failure
- `Connection refused`
  - classify as connection refused
- `Connection closed`
  - classify as connection refused or early remote close, depending on the local pattern
- no URL in stdout or stderr and no stronger signal
  - classify as no URL produced

## Reporting rule

If the logs support a stronger startup reason, use it. Only use the generic no-URL reason when the logs do not support anything more specific.
