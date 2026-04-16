# Orange Pi Live Failure Taxonomy

## `network_peer_isolation_suspected`

Use when the board can reach its gateway, but direct peer reachability to the workstation still indicates isolation. This is a network-mode problem, not a provider startup problem.

Expected evidence:

1. board-side gateway reachability
2. neighbor evidence such as missing, `FAILED`, or `INCOMPLETE`
3. failed direct route probes

## `provider_startup_failed`

Use when a tunnel provider fails before a usable URL is established.

Expected evidence:

1. no usable provider URL
2. provider stdout and stderr with a startup reason
3. provider-specific attempt summary artifact

## `transport_burn_in_failed`

Use when admission succeeds but the chosen transport degrades during burn-in.

Expected evidence:

1. admission success before burn-in
2. later sample failures
3. burn-in summary showing the failing surfaces

## `workstation_admission`

Use when the workstation-side surfaces are not healthy enough to admit the route or provider.

Expected evidence:

1. workstation-side non-200 or missing required surfaces
2. recorded workstation status, snapshot, and pose codes

## `board_admission`

Use when the board cannot be admitted onto the intended transport path.

Expected evidence:

1. hotspot not visible
2. hotspot activation failed
3. `wlan0` missing IPv4
4. default route not moved to `wlan0`
5. direct-LAN precondition not satisfied
