# MITRE ATT&CK Mapping

Mappings describe observed behavior, not attacker attribution.

| Scenario | Data source | Event ID / rule | Detection logic | MITRE technique | Analyst note |
|---|---|---|---|---|---|
| PowerShell with monitored options | Sysmon Process Create | Event ID `1`, rule `100120` | `powershell.exe` command line includes a monitored switch | `T1059.001` PowerShell | Validate the complete command and parent process; switches can be legitimate. |
| File created in lab path | Sysmon File Create | Event ID `11`, rule `100110` | Target filename starts with `C:\SOC-Lab\` | No technique assigned | File creation alone is generic telemetry and needs behavioral context. |
| File added to monitored path | Wazuh FIM | Rule `554` -> `100100` | FIM reports an added file under `C:\SOC-Lab` | No technique assigned | Confirm change authorization and correlate with process telemetry. |
| Alternate data stream | Sysmon File Create Stream Hash | Event ID `15`, rule `100115` | Target filename contains a named stream in the lab path | `T1564.004` NTFS File Attributes | ADS use is not automatically malicious; inspect stream name and creator. |
| Simulated download behavior | Sysmon process/network/file events | Not enabled by default | Add only when a controlled test proves file transfer behavior | `T1105` Ingress Tool Transfer | Do not assign based only on a PowerShell process or URL string. |
| File timestamp modification | Sysmon File Creation Time Changed | Event ID `2`, future rule | Add only when timestamp manipulation is observed | `T1070.006` Timestomp | Planned scenario; no current custom rule claims this detection. |

