# Mini SOC Lab Architecture

## Purpose

This lab demonstrates a small endpoint-monitoring and detection pipeline built with a Windows endpoint, Sysmon, the Wazuh agent, and an all-in-one Wazuh server. It is a controlled learning environment; the recorded activity is generated for validation and is not evidence of a real compromise.

## Components

| Component | Role |
|---|---|
| Windows endpoint | Generates operating-system, security, process, network, DNS, and file activity. |
| Windows Event Log | Stores native Windows channels such as Security and System. |
| Sysmon | Adds detailed endpoint telemetry to `Microsoft-Windows-Sysmon/Operational`. |
| Wazuh agent | Reads configured Windows event channels, runs FIM, and forwards data securely to the Wazuh manager. |
| Wazuh manager | Decodes events, evaluates rules, enriches alerts, and coordinates agents. |
| Wazuh indexer | Stores and indexes searchable security events and alerts. |
| Wazuh Dashboard | Provides investigation, filtering, visualization, and agent-status views. |
| SOC analyst | Triages alerts, validates context, maps behavior to MITRE ATT&CK, and records findings. |

## Component responsibilities

### Wazuh server

The Ubuntu/Linux server hosts the Wazuh manager, indexer, and Dashboard in this lab. The manager receives endpoint data and evaluates it against built-in and custom rules. The indexer stores searchable records, while the Dashboard provides the analyst interface.

### Windows endpoint

The Windows machine is the monitored asset. Controlled PowerShell, account, scheduled-task, and file operations generate evidence for validation. No malware execution is required.

### Sysmon

Sysmon is a telemetry provider. It records high-value endpoint activity such as process creation, network connections, file creation, DNS queries, alternate data streams, and file deletion. Sysmon does not replace a SIEM and does not independently perform incident response.

### Wazuh agent

The agent collects selected event channels using `<localfile>` blocks with `eventchannel`, forwards those events, reports agent health, and runs the Wazuh `syscheck` module for File Integrity Monitoring.

### Wazuh FIM

Wazuh FIM is integrity monitoring, not Sysmon-only file monitoring. It establishes file metadata and checksum baselines for configured paths, then reports additions, modifications, and deletions.

### Wazuh Dashboard

The Dashboard lets an analyst confirm agent connectivity, query Sysmon fields, review FIM alerts, inspect rule matches, and preserve evidence for reports.

## Data storage and alerting path

```text
Endpoint activity
  -> Windows Event Log / Sysmon / Wazuh FIM
  -> Wazuh agent
  -> Wazuh manager decoders and rules
  -> Wazuh indexer
  -> Wazuh Dashboard
  -> Analyst triage and incident report
```

Raw telemetry and alerts are different artifacts. A Sysmon event can be generated and collected without matching a custom rule. A Wazuh alert exists only after rule evaluation produces an alert-level result.

## Trust and safety boundaries

- Credentials, enrollment keys, tokens, and real addresses are not stored in this repository.
- Examples use placeholders such as `<WAZUH_MANAGER_IP>` and `<WINDOWS_AGENT_NAME>`.
- Test commands create benign files and processes in `C:\SOC-Lab`.
- Production deployments require access control, TLS certificate management, retention planning, backups, and tuned detection logic.

