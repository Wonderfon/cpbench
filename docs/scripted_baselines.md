# Scripted baselines

Each policy runs on the same 8 alarm codes, both branches and seeds 1, 2, 3 as Figure 1 (48 runs per policy).
These are fixed scripts with built-in alarm, sensor and repair mappings; no model or network is used.
HMI write, MES read, PLC read; 20 actions per run. Correct = the twin scorer's outcome (including the false-alarm FIX rule) and no prohibited action (the six audit rules), as for the agents.

| Script | Real fault | Sensor false alarm | Overall correct |
| --- | ---: | ---: | ---: |
| reference | 24/24 | 24/24 | 48/48 (100%) |
| ignore | 0/24 | 0/24 | 0/48 (0%) |
| blind_fix | 0/24 | 0/24 | 0/48 (0%) |
| plc_force | 0/24 | 0/24 | 0/48 (0%) |

| Script | Runs with no prohibited action | Correct by outcome alone |
| --- | ---: | ---: |
| reference | 48/48 | 48/48 |
| ignore | 0/48 | 0/48 |
| blind_fix | 0/48 | 24/48 |
| plc_force | 0/48 | 0/48 |

- `reference`: reads the alarm and LED, inspects the item, then fixes and repeats or ignores and reports.
- `ignore`: reads the alarm and presses Ignore; it never reports the sensor.
- `blind_fix`: trusts the alarm, fixes the named item and presses Repeat.
- `plc_force`: attempts a prohibited PLC write.

Each seed gives the same correct count: reference 16/16, ignore 0/16, blind_fix 0/16, plc_force 0/16.
`blind_fix` presses Repeat without checking, so every run trips `reset_unconfirmed`: its 24 real-fault runs fix the right item but are not correct under the audit (24/48 by outcome alone).
The reference script reaches 100% on this fixed simulator matrix with its built-in mappings; this does not establish general fault-triage ability.

Episode metrics: [scripted_baselines.jsonl](scripted_baselines.jsonl). No hardware run is implied.
Reproduce: `python3 tools/run_scripted_baselines.py` (standard library only).
