---
name: orangepi-live-evidence-reporting
description: Use when turning real Orange Pi live validation runs into operator-grade evidence and a final report, especially when you must extract fields from fresh run directories, summarize test commands and results, confirm dual validation execution, and end with either success or an exact classified failure.
---

# Orangepi Live Evidence Reporting

## Overview

This skill standardizes how to collect evidence from fresh Orange Pi live runs and turn it into a precise final report. It is for sessions where command output, run artifacts, classification labels, and operator-facing conclusions matter more than code changes.

## When to Use

- You already ran one or more real Orange Pi live commands and need to produce the final operator report.
- You need to inspect fresh run directories under `logs/orangepi-com10-live/`.
- The user asked for exact test commands, exact results, and exact report fields rather than a loose summary.
- The run may have ended in `success` or one of the classified failures and the distinction must stay explicit.

Do not use this skill to repair transport or runtime behavior itself. Use it after the runs already happened.

## Non-Negotiables

- Only report from fresh artifacts you actually inspected.
- Do not infer that a validation ran unless you have the executed command and its resulting run directory.
- Preserve the exact classified failure labels when a run did not succeed.
- Distinguish "command completed" from "artifact proved success". Both matter.
- If evidence is missing, say what is missing instead of filling gaps with guesses.

## Evidence Collection Order

### 1. Confirm the exact commands that were run

Collect:

- targeted tests
- broader regression suites
- real validation commands

For each command, capture whether it passed, failed, or was not run.

### 2. Identify the latest fresh run directories

At minimum, locate:

- the latest `-RequireDirectIngest:$true` run, if required
- the latest default no-arg run, if required

Use the actual directory paths in the final report.

### 3. Read the required artifacts

For each run, inspect:

- `final-report.json`
- `live-health.json` when present
- `wifi-retarget.json` when present
- `serial-session.log` when relevant to the failure or recovery story

If tunnel fallback was involved, also inspect:

- `localtunnel-attempts.json`
- `localhostrun-attempts.json`
- `auto-tunnel-selected.json`

### 4. Extract the key final-report fields

For every reported run, extract:

- `status`
- `selected_transport_mode`
- `network_admission_reason`
- `recommended_next_action`

Also note:

- whether `provider_attempt_summary` exists
- whether it is empty because direct-LAN succeeded or populated because provider fallback was attempted

### 5. Decide the top-level conclusion

The session conclusion must end as one of:

- `success`
- `network_peer_isolation_suspected`
- `provider_startup_failed`
- `transport_burn_in_failed`
- `workstation_admission`
- `board_admission`

Do not invent alternate top-level labels.

## Final Report Shape

The final answer should include:

1. files actually modified
2. test commands run
3. result of each command
4. whether `Xiaomi 14` direct-LAN validation was executed
5. whether the default no-arg validation was executed
6. latest run directory for each real validation
7. extracted `final-report.json` key fields for each run
8. whether `provider_attempt_summary` was written correctly
9. final conclusion: `success` or the exact classified failure

Keep it concise, but do not omit any required field.

## Common Mistakes

- Reporting the newest directory without checking whether it belongs to the required command.
- Saying "passed" because the script exited cleanly without reading `final-report.json`.
- Losing the distinction between a direct-LAN success and an empty provider summary.
- Collapsing specific failure labels into vague phrases like "transport failed".
- Omitting whether the `Xiaomi 14` validation and default validation were both executed.

## References

- Use [artifact-checklist.md](./references/artifact-checklist.md) for the minimum artifact set.
- Use [report-template.md](./references/report-template.md) for a reusable final-report layout.
