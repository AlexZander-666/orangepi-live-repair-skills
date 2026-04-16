# Serial Output Cleaning Checklist

Before deriving any status field from serial output, remove:

1. ANSI escape sequences
2. command echo lines
3. prompt lines
4. wrapper scaffolding such as `rc=$?`
5. completion marker lines

## Why this matters

If raw serial output is parsed directly, downstream logic can misread:

- active connection names
- IPv4 addresses
- default gateways
- provider URLs
- readiness markers

The result is false negatives, false positives, or both.

## Minimum cleaned-output rule

Only the semantic payload should remain after cleaning. If the result still contains prompt text or marker lines, keep cleaning before you classify anything.
