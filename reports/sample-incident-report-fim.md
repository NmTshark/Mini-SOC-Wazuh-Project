# Sample Incident Report - Unauthorized File Modification

## Executive summary

On June 4, 2026, Wazuh File Integrity Monitoring reported creation and modification of `C:\SOC-Lab\policy-test.txt` on the controlled endpoint `WIN-LAB-01`. The analyst validated the file lifecycle and correlated the events with the approved test window. No production data or security control was affected. The activity was classified as a controlled validation.

## Detection source

| Field | Value |
|---|---|
| First detection | 2026-06-04 09:15:12 UTC |
| Product | Wazuh |
| Data source | Wazuh FIM/syscheck |
| Rules | `554` file added, `550` checksum changed, `553` file deleted |
| Monitored path | `C:\SOC-Lab` |

## Timeline

| Time (UTC) | Event | Analyst interpretation |
|---|---|---|
| 09:15:12 | `policy-test.txt` added | New file appeared in the monitored directory. |
| 09:15:28 | File checksum changed | Content was modified after creation. |
| 09:16:03 | Analyst began triage | Host, path, rule sequence, and test authorization reviewed. |
| 09:17:41 | File deleted | Final lifecycle stage observed. |
| 09:20:00 | Case closed | Activity matched the approved validation plan. |

## Affected host

| Field | Value |
|---|---|
| Hostname | `WIN-LAB-01` |
| Asset type | Windows lab endpoint |
| Agent status | Active during the event |
| User | Authorized lab administrator |

## Alert details

The Wazuh FIM sequence showed an added file, a changed checksum, and deletion under the configured real-time monitoring path. Primary fields included `syscheck.path`, `syscheck.event`, `rule.id`, `rule.level`, and `agent.name`.

The FIM events establish integrity changes. They do not independently identify malicious intent. Process attribution should be correlated with Sysmon or other endpoint telemetry when required.

## Evidence

- Dashboard filter: `agent.name:"WIN-LAB-01" AND rule.id:(550 OR 553 OR 554)`
- File path: `C:\SOC-Lab\policy-test.txt`
- Screenshot evidence: [`screenshots/phase-04/`](../screenshots/phase-04/)
- Validation guide: [`phase-04-detection-validation.md`](../lab-guides/phase-04-detection-validation.md)

## Analyst triage notes

1. Confirmed the alert originated from the expected lab agent.
2. Confirmed all events occurred inside the dedicated test directory.
3. Reviewed the ordered create, modify, and delete sequence.
4. Confirmed the time window matched an approved validation exercise.
5. Found no evidence of persistence, credential access, malware execution, or impact outside the lab path.

## MITRE ATT&CK mapping

No ATT&CK technique was assigned solely from the generic FIM lifecycle. A technique should be added only when corroborating behavior supports a specific adversary action.

## Impact

No operational impact occurred. The changed file was a disposable text file in a dedicated lab directory.

## Containment

No containment was required. The test file was deleted as part of the lifecycle exercise.

## Remediation

- Retain monitoring for the dedicated path.
- Correlate future unexpected FIM changes with Sysmon process telemetry.
- Tune authorized recurring changes if they create excessive noise.

## Lessons learned

- Wazuh FIM successfully captured the complete lifecycle.
- A monitored-path alert requires change context before escalation.
- FIM and Sysmon provide complementary integrity and process evidence.

## Final classification

**Controlled validation / benign positive**
