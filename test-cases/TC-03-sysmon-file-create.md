# TC-03 - Sysmon File Create

## Test case ID

`TC-03`

## Objective

Create a benign file in `C:\SOC-Lab` and validate Sysmon Event ID `11`.

## Commands

```powershell
New-Item -ItemType Directory -Force "C:\SOC-Lab" | Out-Null
Set-Content "C:\SOC-Lab\sysmon-file-create.txt" "SOC-LAB"

Get-WinEvent `
  -FilterHashtable @{
    LogName = "Microsoft-Windows-Sysmon/Operational"
    Id = 11
  } `
  -MaxEvents 10
```

## Expected Windows evidence

- Sysmon Event ID `11`.
- `TargetFilename` is `C:\SOC-Lab\sysmon-file-create.txt`.
- Creator image, process ID, process GUID, and timestamp are present.

## Expected Wazuh evidence

- Collected Event ID `11`.
- Target filename and creator context are searchable.
- Custom rule `100110` matches if deployed.

## Screenshot checklist

- [ ] File creation command
- [ ] Local Event ID `11`
- [ ] Wazuh event fields
- [ ] Rule `100110` alert

## Pass/fail criteria

- **Pass:** Event ID `11` is generated, collected, and contains the expected path.
- **Fail:** The file exists but no matching Sysmon evidence reaches Wazuh.

## Cleanup

```powershell
Remove-Item "C:\SOC-Lab\sysmon-file-create.txt" -Force -ErrorAction SilentlyContinue
```

