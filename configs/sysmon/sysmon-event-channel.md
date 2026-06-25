# Sysmon Event Channel

Sysmon writes its operational events to:

```text
Applications and Services Logs
  -> Microsoft
  -> Windows
  -> Sysmon
  -> Operational
```

The full channel name used by Wazuh is:

```text
Microsoft-Windows-Sysmon/Operational
```

## Useful checks

```powershell
Get-WinEvent -ListLog "Microsoft-Windows-Sysmon/Operational"

Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 10 |
  Select-Object TimeCreated, Id, ProviderName

wevtutil gli Microsoft-Windows-Sysmon/Operational
```

## Event IDs used in this project

| Event ID | Sysmon event |
|---:|---|
| 1 | Process Create |
| 3 | Network Connect |
| 11 | File Create |
| 15 | File Create Stream Hash |
| 22 | DNS Query |
| 23 | File Delete archived |
| 26 | File Delete detected |

Event availability depends on the installed Sysmon version and active configuration.
