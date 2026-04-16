# Orange Pi Live Final Report Template

Use this as a structure, but replace every placeholder with inspected evidence.

```text
Modified files:
- <path>

Test commands and results:
- <command> -> PASS|FAIL

Real validations:
- Xiaomi 14 direct-LAN validation executed: yes|no
- Default no-arg validation executed: yes|no

Run directories:
- Direct-LAN run: <path or not run>
- Default run: <path or not run>

Final-report fields:
- Direct-LAN run:
  - status: <value>
  - selected_transport_mode: <value>
  - network_admission_reason: <value>
  - recommended_next_action: <value>
- Default run:
  - status: <value>
  - selected_transport_mode: <value>
  - network_admission_reason: <value>
  - recommended_next_action: <value>

Provider attempt summary:
- Direct-LAN run: present|missing, empty|non-empty
- Default run: present|missing, empty|non-empty

Final conclusion:
- success|network_peer_isolation_suspected|provider_startup_failed|transport_burn_in_failed|workstation_admission|board_admission
```
