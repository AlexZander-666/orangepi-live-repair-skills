# Orange Pi Live Direct-LAN Repair Command Checklist

## Read first

1. Read the controlling design and implementation plan for the current repo.
2. Confirm the scope is transport-focused.
3. Confirm the run must force `Xiaomi 14` before continuing.

## TDD loop

1. Add one failing contract test for the next smallest behavior.
2. Run the targeted test and confirm it fails for the expected reason.
3. Implement the minimum code change.
4. Rerun the targeted test.
5. Rerun the broader regression suite for the touched script.

## Real validation

Run these after the regression suite is green:

```powershell
powershell -ExecutionPolicy Bypass -File scripts/start_orangepi_com10_live.ps1 -RequireDirectIngest:$true
powershell -ExecutionPolicy Bypass -File scripts/start_orangepi_com10_live.ps1
```

## Required artifact review

Inspect:

1. `serial-session.log`
2. `wifi-retarget.json`
3. `live-health.json`
4. `final-report.json`
5. provider attempt artifacts such as `localtunnel-attempts.json`, `localhostrun-attempts.json`, or `auto-tunnel-selected.json`

## Success gate

Only declare success if:

1. `remote_wifi_retarget` succeeds.
2. `selected_transport_mode` is correct.
3. `final-report.json` is written.
4. Each required real run has a fresh run directory.
