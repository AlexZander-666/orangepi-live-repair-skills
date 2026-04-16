# Orange Pi Live Artifact Checklist

## Minimum command evidence

Collect:

1. targeted tests run during TDD
2. broader regression command for the touched surface
3. real validation command with `-RequireDirectIngest:$true`, when required
4. real validation command with default no-arg execution, when required

## Minimum runtime artifacts per reported run

Inspect:

1. `final-report.json`
2. `live-health.json`, if present
3. `wifi-retarget.json`, if present
4. `serial-session.log`, if the run failed or required shell recovery

## Provider artifacts

Inspect these when provider fallback was attempted:

1. `localtunnel-attempts.json`
2. `localhostrun-attempts.json`
3. `auto-tunnel-selected.json`

## Required extracted fields

For each `final-report.json`, capture:

1. `status`
2. `selected_transport_mode`
3. `network_admission_reason`
4. `recommended_next_action`
5. `provider_attempt_summary`

## Final answer must say

1. what changed
2. what ran
3. what passed or failed
4. which run directories are being cited
5. the final conclusion label
