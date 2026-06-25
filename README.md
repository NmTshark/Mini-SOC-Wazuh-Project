# Enterprise Mini SOC Lab with Wazuh and Sysmon

A portfolio-ready Blue Team lab that follows Windows endpoint activity from telemetry generation through Wazuh collection, detection validation, analyst triage, and incident reporting. All scenarios are controlled and use benign test commands; this repository does not claim real malware execution or attacker activity.

## SOC workflow

```text
Endpoint Activity
  -> Sysmon / Windows Logs / Wazuh FIM
  -> Wazuh Agent
  -> Wazuh Manager
  -> Wazuh Indexer
  -> Wazuh Dashboard
  -> Alert Triage
  -> Incident Report
```

## Lab architecture

| Layer | Component | Responsibility |
|---|---|---|
| Endpoint | Windows 10/11 | Generates controlled process, account, task, and file activity |
| Telemetry | Sysmon | Records detailed process, network, DNS, and file events |
| Native logging | Windows Event Log | Stores Security, System, and Operational channels |
| Integrity | Wazuh FIM/syscheck | Detects added, modified, and deleted files relative to a baseline |
| Collection | Wazuh Agent | Collects event channels, runs FIM, and forwards endpoint data |
| Analysis | Wazuh Manager | Decodes events and evaluates built-in and custom rules |
| Storage | Wazuh Indexer | Stores searchable security events and alerts |
| Investigation | Wazuh Dashboard | Supports filtering, triage, evidence review, and agent monitoring |
| Analyst | SOC workflow | Validates alerts, maps behavior, and produces incident reports |

See [architecture details](architecture/architecture.md), [data flow](architecture/data-flow.md), and the [Mermaid diagram](architecture/mermaid-architecture.md).

## Project status

| Phase | Scope | Status |
|---|---|---|
| Phase 01 | Wazuh Dashboard and server setup | Completed |
| Phase 02 | Windows Wazuh agent deployment | Completed |
| Phase 03 | Windows log collection, Sysmon telemetry, and FIM | Completed |
| Phase 04 | Detection validation and evidence | Completed |

## Tools used

- Wazuh Server: manager, indexer, and Dashboard
- Wazuh Agent for Windows
- Microsoft Sysmon
- Windows Event Log
- Wazuh File Integrity Monitoring
- PowerShell
- Ubuntu/Linux server
- MITRE ATT&CK
- Git and Markdown

## Detection scenarios

| Scenario | Primary source | Evidence or rule | State |
|---|---|---|---|
| Windows agent connectivity | Wazuh agent health | Active endpoint and keepalive | Validated |
| PowerShell process creation | Sysmon Event ID `1` | Process image and command line | Validated |
| File creation | Sysmon Event ID `11` | Target filename and creator process | Documented |
| File lifecycle | Wazuh FIM | Rules `554`, `550`, and `553` | Validated |
| Alternate data stream | Sysmon Event ID `15` | Custom rule `100115` | Ready to validate |
| Local account creation | Windows Security | Event ID `4720` | Validated |
| Scheduled task persistence | Task Scheduler Operational | Task XML collected by Wazuh | Validated |
| Defender tamper command simulation | Sysmon Event ID `1` | Telemetry only; Defender remains enabled | Validated |

The [Phase 04 validation guide](lab-guides/phase-04-detection-validation.md) explains which scenarios produced raw events, Wazuh alerts, or telemetry-only results.

## Evidence

- [Phase 01 - Wazuh Dashboard](screenshots/phase-01/)
- [Phase 02 - Windows agent](screenshots/phase-02/)
- [Phase 03 - Windows logging and FIM setup evidence](screenshots/phase-03/)
- [Phase 04 - Detection validation evidence](screenshots/phase-04/)
- [Detection scenario evidence placeholder](screenshots/detection-scenarios/)

Screenshots are supporting evidence, not substitutes for repeatable commands and pass/fail criteria. New screenshots should be reviewed for credentials, tokens, private addresses, and personal data before commit.

## Skills demonstrated

- SIEM deployment and administration
- Windows endpoint onboarding
- Sysmon configuration and event analysis
- Windows eventchannel collection
- Wazuh FIM configuration and integrity validation
- Detection engineering with custom XML rules
- Rule validation with `wazuh-logtest`
- MITRE ATT&CK mapping
- Alert triage and evidence handling
- Incident report writing
- Technical documentation and secret hygiene

## Repository structure

```text
.
├── architecture/
│   ├── architecture.md
│   ├── data-flow.md
│   └── mermaid-architecture.md
├── configs/
│   ├── sysmon/
│   │   ├── sysmon-event-channel.md
│   │   └── sysmonconfig.xml
│   └── wazuh/
│       ├── syscheck-fim-config.xml
│       └── windows-agent-ossec.conf
├── detection-rules/
│   ├── local_rules.xml
│   ├── mitre-mapping.md
│   ├── rule-explanation.md
│   └── rule-validation.md
├── lab-guides/
│   ├── phase-01-wazuh-dashboard-setup.md
│   ├── phase-02-wazuh-agent.md
│   ├── phase-03-setup-window.md
│   ├── phase-04-detection-validation.md
│   └── validation-commands.md
├── reports/
│   ├── incident-report-template.md
│   ├── sample-incident-report-fim.md
│   ├── final-project-summary.md
│   └── enterprise-mini-soc-lab-wazuh-sysmon.docx
├── screenshots/
│   ├── phase-01/
│   ├── phase-02/
│   ├── phase-03/
│   ├── phase-04/
│   └── detection-scenarios/
├── test-cases/
│   ├── TC-01-windows-agent-connectivity.md
│   ├── TC-02-sysmon-process-creation.md
│   ├── TC-03-sysmon-file-create.md
│   ├── TC-04-fim-file-added-modified-deleted.md
│   └── TC-05-sysmon-ads-detection.md
├── CHANGELOG.md
├── references.md
└── README.md
```

## How to reproduce

1. Deploy the Wazuh central components using [Phase 01](lab-guides/phase-01-wazuh-dashboard-setup.md).
2. Enroll the Windows endpoint using [Phase 02](lab-guides/phase-02-wazuh-agent.md).
3. Configure Windows log collection, Sysmon telemetry, and FIM using [Phase 03](lab-guides/phase-03-setup-window.md).
4. Run and review the documented scenarios in [Phase 04](lab-guides/phase-04-detection-validation.md).
5. Review the [custom rules](detection-rules/local_rules.xml) and supporting test cases as optional extensions.
6. Record findings with the [incident report template](reports/incident-report-template.md).

Use placeholders when reproducing the lab in a different environment.

## What I learned

- Telemetry collection and alert generation are separate stages.
- Sysmon provides process-centric file telemetry, while Wazuh FIM monitors integrity against a baseline.
- Good detections depend on stable fields, narrow scope, known false positives, and repeatable tests.
- MITRE ATT&CK mappings must describe observed behavior rather than inflate a result.
- An analyst report should separate facts, interpretation, impact, and final classification.

## Limitations and next steps

- The lab currently uses one Windows endpoint and an all-in-one Wazuh server.
- Rules are scoped for learning and require production tuning.
- ADS and all custom rules should be validated against archived real events.
- Future work can add centralized agent groups, authentication detections, dashboards, case management, retention planning, and controlled network telemetry.

## Safety and privacy

All commands and evidence are intended for a dedicated lab. Any addresses, hostnames, or credentials visible in retained legacy evidence are simulated lab values and are not production assets.

## References

See [references.md](references.md) for official Wazuh, Microsoft Sysmon, and MITRE ATT&CK documentation.
