# Final Project Summary

## Project

**Enterprise Mini SOC Lab with Wazuh, Sysmon, Windows Endpoint Monitoring, File Integrity Monitoring, and Detection Engineering**

This portfolio project implements an endpoint-to-SIEM workflow using a Windows test machine and an all-in-one Wazuh server. It documents deployment, telemetry collection, file integrity monitoring, custom detection logic, repeatable test cases, MITRE ATT&CK mapping, alert triage, and incident reporting.

## Delivered capabilities

- Wazuh manager, indexer, and Dashboard deployment
- Windows agent enrollment and connectivity validation
- Sysmon installation and Operational channel verification
- Central collection of Sysmon Event IDs `1`, `11`, and `15`
- Wazuh FIM monitoring for added, modified, and deleted files
- Custom Wazuh rules in the `100000–120000` range
- Safe PowerShell, file-create, FIM, and ADS validation scenarios
- Rule testing guidance using `wazuh-logtest`
- Evidence-driven incident reporting

## SOC workflow demonstrated

```text
Endpoint activity
  -> Windows / Sysmon / FIM telemetry
  -> Wazuh agent
  -> Wazuh manager rules and decoders
  -> Wazuh indexer
  -> Wazuh Dashboard
  -> Alert triage
  -> Incident report
```

## Skills demonstrated

- SIEM administration and endpoint onboarding
- Windows Event Log and Sysmon telemetry analysis
- Wazuh `eventchannel` and `syscheck` configuration
- Detection-rule design and false-positive analysis
- MITRE ATT&CK mapping with evidence boundaries
- Dashboard searching and alert validation
- Test-case design and technical documentation
- Incident triage and report writing
- Git-based portfolio organization and secret hygiene

## Portfolio value

The repository shows not only that the tools were installed, but also how telemetry is generated, forwarded, decoded, searched, validated, and converted into an analyst report. Each test uses controlled, non-destructive commands suitable for demonstration in an internship or OJT interview.

## Limitations

- The lab uses one Windows endpoint and an all-in-one Wazuh server.
- Rule thresholds and allowlists are not production tuned.
- Network IDS, threat-intelligence enrichment, case management, and automated response are outside the documented core.
- Screenshots represent a lab point in time and may differ across Wazuh versions.

## Next improvements

1. Deploy and validate every custom rule with archived sample events.
2. Add Windows Security authentication and account-management scenarios.
3. Add dashboard visualizations and detection coverage metrics.
4. Introduce centralized agent groups and configuration management.
5. Integrate a ticketing or case-management workflow.
