# Incident Report Template

## Executive summary

Summarize what was detected, when it occurred, the affected asset, why it matters, and the current status. State clearly whether the activity is confirmed, suspected, benign, or a controlled validation.

## Detection source

| Field | Value |
|---|---|
| Detection time | `<UTC timestamp>` |
| Product | Wazuh |
| Data source | `<Windows Security / Sysmon / Wazuh FIM>` |
| Rule ID | `<rule ID>` |
| Rule description | `<description>` |
| Alert severity | `<level>` |

## Timeline

| Time (UTC) | Event | Evidence |
|---|---|---|
| `<time>` | Initial activity | `<event or alert reference>` |
| `<time>` | Analyst triage | `<query or finding>` |
| `<time>` | Containment or closure | `<action>` |

## Affected host

| Field | Value |
|---|---|
| Hostname | `<hostname>` |
| Agent name | `<agent name>` |
| Operating system | `<OS>` |
| User | `<user or unknown>` |
| Business owner | `<owner or lab>` |

## Alert details

Document the event ID, process image, command line, parent process, target file, hashes, network destination, and other relevant fields. Distinguish raw events from rule-generated alerts.

## Evidence

- Dashboard query:
- Event JSON reference:
- Screenshot path:
- Endpoint verification:
- Related events:

Do not embed passwords, tokens, enrollment keys, or unnecessary personal information.

## MITRE ATT&CK mapping

| Technique | Name | Mapping rationale |
|---|---|---|
| `<ID>` | `<name>` | `<observed behavior supporting the mapping>` |

## Impact

Describe confirmed or potential effects on confidentiality, integrity, and availability. Avoid claiming impact that the evidence does not support.

## Containment

Record approved containment actions, responsible person, timestamps, and results.

## Remediation

List root-cause corrections, configuration changes, patching, credential actions, and detection tuning.

## Lessons learned

Document visibility gaps, useful telemetry, false positives, process improvements, and follow-up owners.

## Final classification

`<True positive / Benign positive / False positive / Controlled validation>`

