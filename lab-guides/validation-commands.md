# Validation Command Reference

Run commands only in the controlled lab and use an elevated shell where noted.

## Wazuh server

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
sudo tail -n 100 /var/ossec/logs/ossec.log
sudo /var/ossec/bin/wazuh-logtest
```

## Windows Wazuh agent

```powershell
Get-Service WazuhSvc
Restart-Service WazuhSvc
Get-Content "C:\Program Files (x86)\ossec-agent\ossec.log" -Tail 100
```

## Sysmon

```powershell
Get-Service Sysmon64
& "C:\Tools\Sysmon\Sysmon64.exe" -c
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 20
```

## Safe process and file tests

```powershell
New-Item -ItemType Directory -Force "C:\SOC-Lab" | Out-Null
powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB process test'"
Set-Content "C:\SOC-Lab\validation.txt" "created"
Add-Content "C:\SOC-Lab\validation.txt" "modified"
Remove-Item "C:\SOC-Lab\validation.txt" -Force
```

## Dashboard searches

```text
agent.name:"<WINDOWS_AGENT_NAME>"
data.win.system.eventID:"1"
data.win.system.eventID:"11"
data.win.system.eventID:"15"
rule.id:(550 OR 553 OR 554)
rule.id:(100110 OR 100111 OR 100115 OR 100120)
```

Field names can vary with Wazuh version, index pattern, and decoder output. Expand one matching event and adjust the query to the fields present in the lab.
