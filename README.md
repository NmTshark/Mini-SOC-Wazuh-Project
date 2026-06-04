# Enterprise Mini SOC Lab with Wazuh and Sysmon

## Overview

This project demonstrates the deployment of a mini Security Operations Center lab using Wazuh, Sysmon, and a Windows endpoint. The goal is to collect endpoint telemetry, monitor security events, detect suspicious activity, and analyze alerts through the Wazuh Dashboard.

## Lab Architecture

| Component | Description |
|---|---|
| Wazuh Server | Central SIEM server running Wazuh Manager, Indexer, and Dashboard |
| Windows Endpoint | Monitored Windows client machine |
| Wazuh Agent | Sends endpoint logs to the Wazuh Server |
| Sysmon | Provides detailed Windows endpoint telemetry |
| Wazuh Dashboard | Used for alert monitoring, investigation, and visualization |

## Project Progress

| Phase | Description | Status |
|---|---|---|
| Phase 01 | Wazuh Dashboard/Admin Setup | Completed |
| Phase 02 | Windows Agent Setup | Pending |
| Phase 03 | Sysmon Setup | Pending |
| Phase 04 | Sysmon Log Collection | Pending |
| Phase 05 | Detection Scenarios | Pending |
| Phase 06 | Custom Wazuh Rules | Pending |
| Phase 07 | Incident Reports | Pending |
| Phase 08 | Final Documentation | Pending |

## Skills Demonstrated

- SIEM deployment
- Wazuh Dashboard administration
- Endpoint monitoring
- Windows log collection
- Sysmon telemetry
- Detection engineering basics
- MITRE ATT&CK mapping
- Alert triage
- Incident reporting

## Repository Structure

```text
architecture/       Lab architecture and overview
installation/       Phase-based setup documentation
detection-rules/    Custom Wazuh rules and explanations
test-cases/         Detection validation scenarios
reports/            Incident reports and final project summary
screenshots/        Evidence from the lab