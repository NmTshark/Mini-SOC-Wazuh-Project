# TC-02 - Sysmon Process Creation

## Test case ID

`TC-02`

## Objective

Generate a benign PowerShell process and confirm Sysmon Event ID `1` locally and in Wazuh.

## Commands

```powershell
powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB process creation test'"

Get-WinEvent `
  -FilterHashtable @{
    LogName = "Microsoft-Windows-Sysmon/Operational"
    Id = 1
  } `
  -MaxEvents 5
```

## Expected Windows evidence

- Sysmon Event ID `1`.
- Image is `powershell.exe`.
- Command line contains `SOC-LAB process creation test`.
- Process ID, process GUID, parent image, user, and hashes are present when configured.

## Expected Wazuh evidence

- Collected Sysmon Event ID `1`.
- Correct agent attribution.
- Searchable image and command-line fields.
- Optional custom rule `100120` match because `-NoProfile` is present.

## Screenshot checklist

- [ ] Test command
- [ ] Local Sysmon Event ID `1`
- [ ] Expanded Wazuh event
- [ ] Custom alert, if rule `100120` is deployed

## Pass/fail criteria

- **Pass:** Matching Event ID `1` exists locally and in Wazuh.
- **Fail:** No local event, no collected event, or required process fields are absent.

