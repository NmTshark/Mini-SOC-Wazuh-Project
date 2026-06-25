# TC-05 - Sysmon Alternate Data Stream Detection

## Test case ID

`TC-05`

## Objective

Create a harmless NTFS alternate data stream in `C:\SOC-Lab` and validate Sysmon Event ID `15`.

## Commands

```powershell
$hostFile = "C:\SOC-Lab\ads-host.txt"

Set-Content $hostFile "visible content"
Set-Content "$hostFile:soc-lab-stream" "controlled ADS validation"

Get-Item -Path $hostFile -Stream *
```

## Expected Windows evidence

- The host file has the default data stream and `soc-lab-stream`.
- Sysmon Event ID `15` records `FileCreateStreamHash`.
- The target filename contains the named stream.

## Expected Wazuh evidence

- Collected Sysmon Event ID `15`.
- Searchable target filename and hash fields where available.
- Custom rule `100115` and MITRE mapping `T1564.004` if deployed.

## Screenshot checklist

- [ ] Safe ADS creation command
- [ ] `Get-Item -Stream *` output
- [ ] Local Sysmon Event ID `15`
- [ ] Wazuh rule `100115` alert

## Pass/fail criteria

- **Pass:** Event ID `15` is collected and the custom rule matches the lab stream.
- **Fail:** The stream exists but no Sysmon/Wazuh evidence is available.

## Cleanup

```powershell
Remove-Item "C:\SOC-Lab\ads-host.txt" -Force -ErrorAction SilentlyContinue
```
