---
name: orangepi-live-direct-lan-repair
description: Use when recovering or validating a COM10-attached Orange Pi live runtime that must force onto a known-good Xiaomi 14 hotspot before continuing, especially when direct-LAN ingest, serial shell control, classified transport failures, strict TDD, and dual real-validation runs are required.
---

# Orangepi Live Direct Lan Repair

## Overview

This skill standardizes high-discipline recovery of a CareSense Orange Pi live runtime when transport truth is the main problem. It keeps direct-LAN as the primary operating mode, forces the board onto the known-good `Xiaomi 14` hotspot first, and requires classified failures, preserved artifacts, strict TDD, and fresh real validation.

## When to Use

- The board is managed through `COM10 @ 115200` and SSH is not the primary control path.
- The run must force `wlan0` onto `Xiaomi 14` before any runtime, route, or ingest work continues.
- You need to debug `serial-session.log`, `wifi-retarget.json`, `live-health.json`, or `final-report.json`.
- The failure is likely in transport operating modes, provider startup truth, or board/workstation admission rather than backend TTL or camera heuristics.
- The task explicitly requires strict TDD and real command verification, not analysis-only advice.

Do not use this skill for broad FastAPI refactors, TTL semantics changes, board-side camera heuristic redesign, or unrelated runtime cleanup.

## Non-Negotiables

- Read the controlling design and implementation plan first when the repo provides them.
- Work in the main workspace unless the user explicitly asks for parallel isolated agents.
- Follow strict TDD: write a failing test, run it and confirm the failure, implement the minimum fix, rerun the targeted test, then rerun the broader regression.
- Do not hide failures. Preserve and enhance logs, artifacts, and classified failure output.
- Treat `peer_reachable_direct_lan` as the primary operating mode. Tunnel providers are recovery paths, not the default success story.

## Workflow

### 1. Prove scope before edits

- Confirm the task is transport-focused.
- Identify the exact allowed surface area.
- Refuse scope drift into backend TTL, camera heuristic redesign, or unrelated refactors.

### 2. Stabilize serial shell first

- Before trusting any board-side command result, make sure the serial session is actually in a runnable shell state.
- If logs show repeated bare `>` prompts, treat that as continuation-prompt corruption and recover the shell before debugging Wi-Fi or transport.
- Avoid multiline shell wrappers that can leave the board in an unterminated quoting state.
- Strip shell wrapper noise from parsed serial results so command echo, prompts, and completion markers do not contaminate status fields.

### 3. Force Xiaomi 14 before continuing

- Check board-side Wi-Fi state over serial.
- If a saved `Xiaomi 14` profile exists, try `nmcli connection up 'Xiaomi 14' ifname wlan0` first.
- If there is no saved profile but the SSID is visible, use `nmcli dev wifi connect 'Xiaomi 14' password '<password>' ifname wlan0`.
- Fail fast if the hotspot is not visible, the activation fails, `wlan0` has no IPv4, or the default route does not move onto `wlan0`.
- Write and preserve a board admission artifact such as `wifi-retarget.json`.

### 4. Decide transport operating mode explicitly

- Gather direct-LAN evidence from both workstation and board perspectives.
- If direct route is healthy, classify the run as `peer_reachable_direct_lan`.
- If the gateway is reachable but board-side peer evidence indicates the workstation is isolated, classify `network_peer_isolation_suspected`.
- Only enter tunnel provider logic after direct-LAN evidence is collected and classified.

### 5. Preserve provider startup truth

- Record per-provider attempt summaries in provider-neutral artifacts.
- Distinguish provider startup failures from admission failures and burn-in failures.
- If no URL is produced, parse stdout and stderr into a concrete startup reason instead of returning a generic no-url mystery.

### 6. Run the two real validations

After code and tests are green, run both real commands:

```powershell
powershell -ExecutionPolicy Bypass -File scripts/start_orangepi_com10_live.ps1 -RequireDirectIngest:$true
powershell -ExecutionPolicy Bypass -File scripts/start_orangepi_com10_live.ps1
```

Do not claim success before both runs finish and their artifacts are inspected.

### 7. Report like an operator, not a guesser

- List the files actually changed.
- List every test command and result.
- State whether `Xiaomi 14` direct-LAN validation was executed.
- State whether the current-network default validation was executed.
- Provide the latest run directories.
- Extract `status`, `selected_transport_mode`, `network_admission_reason`, and `recommended_next_action` from each `final-report.json`.
- State whether `provider_attempt_summary` was written correctly.
- End with either `success` or the exact classified failure.

## Classified Failures

- `network_peer_isolation_suspected`
- `provider_startup_failed`
- `transport_burn_in_failed`
- `workstation_admission`
- `board_admission`

Use these exact labels in operator-facing outcomes. Do not collapse them into generic runtime failure wording.

## References

- Use [command-checklist.md](./references/command-checklist.md) for the field-ready command order.
- Use [failure-taxonomy.md](./references/failure-taxonomy.md) for the meaning of each classified failure and the expected evidence.
