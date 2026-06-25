# Custom Rule Explanations

The rules in [`local_rules.xml`](local_rules.xml) use IDs in the recommended custom range and are designed only for the controlled `C:\SOC-Lab` path. Validate actual decoded field names before deployment because versions and integrations can differ.

## Rule 100100 - FIM file added

| Item | Detail |
|---|---|
| Source | Wazuh FIM/syscheck |
| Parent rule | `554` |
| Required field | `syscheck.path` |
| Logic | Match a file added below `C:\SOC-Lab` |
| Severity | 7 |

Possible false positives include authorized testing, software deployment, and administrator-created files. Confirm the path, user context when available, maintenance window, and change ticket.

Test by creating a benign text file after FIM has established its baseline.

## Rule 100110 - Sysmon Event ID 11

| Item | Detail |
|---|---|
| Source | `Microsoft-Windows-Sysmon/Operational` |
| Event | Sysmon Event ID `11` |
| Required field | `win.eventdata.targetFilename` |
| Logic | Match file creation below `C:\SOC-Lab` |
| Severity | 8 |

Expected context can include target filename, image, process ID, process GUID, and UTC time. Normal test scripts and administration tools will create expected matches, so the rule is useful for pipeline validation rather than production threat scoring.

```powershell
Set-Content "C:\SOC-Lab\sysmon-rule-test.txt" "SOC-LAB"
```

## Rule 100115 - Sysmon Event ID 15 / ADS

| Item | Detail |
|---|---|
| Source | Sysmon Operational |
| Event | Sysmon Event ID `15` |
| Required field | `win.eventdata.targetFilename` |
| Logic | Match a named stream below `C:\SOC-Lab` |
| MITRE ATT&CK | `T1564.004` - NTFS File Attributes |
| Severity | 10 |

Legitimate software can use alternate data streams, including Windows attachment metadata. Restricting the rule to the lab path reduces noise but does not establish malicious intent.

Safe test:

```powershell
Set-Content "C:\SOC-Lab\ads-host.txt" "visible content"
Set-Content "C:\SOC-Lab\ads-host.txt:soc-lab-stream" "controlled ADS test"
```

## Rule 100120 - Monitored PowerShell options

| Item | Detail |
|---|---|
| Source | Sysmon Operational |
| Event | Sysmon Event ID `1` |
| Required fields | `win.eventdata.image`, `win.eventdata.commandLine` |
| Logic | PowerShell with `-ExecutionPolicy Bypass`, `-EncodedCommand`, or `-NoProfile` |
| MITRE ATT&CK | `T1059.001` - PowerShell |
| Severity | 8 |

These switches have legitimate administrative uses. Analysts should inspect the complete command line, parent process, user, integrity level, hashes, destination activity, and surrounding timeline.

Safe test:

```powershell
powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB rule test'"
```

## Tuning guidance

- Begin with the dedicated lab path.
- Measure expected matches before increasing severity.
- Add allowlists only for verified, stable business behavior.
- Do not map `T1105` unless the tested behavior actually transfers a file.
- Do not map `T1070.006` unless file-time manipulation is explicitly observed.
