---
name: serial-shell-debug-recovery
description: Use when a UART or serial shell session is polluted by continuation prompts, command echo, ANSI control sequences, or wrapper markers, especially when you must recover the shell safely before continuing hardware, transport, or runtime debugging.
---

# Serial Shell Debug Recovery

## Overview

This skill standardizes recovery of serial shell sessions that look connected but are not actually safe to trust. It covers how to detect polluted shell state, how to recover without making it worse, and how to clean command output so downstream logic sees the real result instead of shell noise.

## When to Use

- The serial log shows repeated bare `>` prompts or similar continuation prompts.
- A command appears to run, but the output is mostly shell echo, prompts, or completion markers.
- The board stays reachable over serial, but every subsequent command times out waiting for a marker.
- You suspect a previous multiline wrapper or unterminated quote left the shell in a half-open state.
- You need a safe pattern for running commands over `System.IO.Ports.SerialPort` or an equivalent UART path.

Do not use this skill for normal SSH session management or for debugging the business logic of commands that already produce clean output.

## Non-Negotiables

- Do not trust a shell just because the serial port is open.
- Recover shell state before debugging Wi-Fi, transport, runtime, or provider logic.
- Prefer the smallest recovery action that restores a runnable prompt.
- Avoid multiline wrappers when the target shell is already suspected to be polluted.
- Clean serial output before deriving status fields from it.

## Workflow

### 1. Confirm whether the shell is actually healthy

Treat these as shell-health warnings:

- repeated bare `>` prompts
- prompts appearing before the expected command marker
- marker text only in echoed command lines, not as executed output
- command output contaminated by prompt lines, ANSI escape sequences, or wrapper scaffolding

If any of these appear, stop assuming later transport failures are the primary bug.

### 2. Recover continuation-prompt corruption first

- If the shell is stuck in a continuation prompt, send `Ctrl+C` and wait for a normal prompt.
- Limit the number of recovery attempts so the logic can fail explicitly instead of looping forever.
- Clear or reset the local buffer after a recovery attempt so stale pollution is not re-parsed as current state.

This is safer than layering new multiline wrappers on top of an already corrupted shell.

### 3. Re-establish a trustworthy ready handshake

- Use a ready command that cannot be mistaken for mere command echo.
- Wait for a completion pattern that includes a concrete marker and an exit code, not just the marker token alone.
- Do not use a quoted multiline marker pattern that can itself trigger continuation-prompt behavior.

### 4. Prefer single-line command wrappers in fragile sessions

- Once a shell is suspected to be fragile, run commands as single-line wrappers with one completion marker.
- Keep quoting simple.
- Avoid heredocs and multiline wrappers unless the shell state is already proven healthy.

### 5. Clean output before parsing it

Remove:

- ANSI control sequences
- echoed command wrapper lines
- prompt lines
- scaffolding lines such as `rc=$?`
- completion marker lines

Only parse status fields from the cleaned result.

### 6. Separate shell recovery from business classification

- Shell-health failures should stay distinct from Wi-Fi admission, transport admission, or provider failures.
- Do not classify network or runtime failures until the shell command path is trustworthy.
- If the shell cannot be recovered, report that directly instead of mislabeling it as a transport problem.

## Common Mistakes

- Treating a serial port connection as proof of a healthy shell.
- Waiting for a marker string that is present only in the echoed command line.
- Reusing a polluted buffer after `Ctrl+C` recovery.
- Parsing status fields from raw serial output instead of cleaned output.
- Using complex multiline shell wrappers in the exact sessions where quoting state is already suspect.

## References

- Use [recovery-patterns.md](./references/recovery-patterns.md) for a compact recovery sequence.
- Use [output-cleaning.md](./references/output-cleaning.md) for the noise-removal checklist.
