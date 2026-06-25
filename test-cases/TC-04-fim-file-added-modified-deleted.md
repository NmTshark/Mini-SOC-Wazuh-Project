# TC-04 - FIM File Added, Modified, and Deleted

## Test case ID

`TC-04`

## Objective

Validate the complete Wazuh FIM lifecycle for a file under `C:\SOC-Lab`.

## Commands

```powershell
$path = "C:\SOC-Lab\fim-lifecycle.txt"

Set-Content $path "created"
Start-Sleep -Seconds 5
Add-Content $path "modified"
Start-Sleep -Seconds 5
Remove-Item $path -Force
```

## Expected Windows evidence

- The file is created, changed, and removed.
- The Wazuh agent log shows that syscheck monitors `C:\SOC-Lab`.

## Expected Wazuh evidence

| State | Common rule |
|---|---:|
| Added | `554` |
| Modified | `550` |
| Deleted | `553` |

Expected fields include `syscheck.path`, `syscheck.event`, `agent.name`, `rule.id`, and available checksum data.

## Screenshot checklist

- [ ] Lifecycle commands
- [ ] FIM alert summary
- [ ] Added event
- [ ] Modified event
- [ ] Deleted event

## Pass/fail criteria

- **Pass:** Added, modified, and deleted states are all visible for the test path.
- **Fail:** One or more lifecycle stages are absent after reasonable ingest delay.

