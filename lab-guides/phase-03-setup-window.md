# Phase 03 - Windows Log Collection & FIM

## Objective

The objective of this phase is to configure the Windows endpoint to send the key telemetry needed for SOC investigation and later detection use cases.

This phase focuses on two main areas:

- Windows Log Collection from important Event Channels
- File Integrity Monitoring (FIM) for selected lab files and folders

This phase prepares telemetry for later detection scenarios such as:

| Use Case | Description |
|---|---|
| UC03 | New local user creation |
| UC04 | User added to local Administrators group |
| UC05 | Suspicious PowerShell activity |
| UC06 | Scheduled task persistence |
| UC08 | Defender tamper attempt |
| UC09 | Unusual outbound connection |


## Step 1 - Verify Wazuh Agent and Sysmon

Run the following commands in PowerShell as Administrator:

```powershell
Get-Service WazuhSvc
Get-Service Sysmon64
```

Expected result:

```text
Status   Name
Running  WazuhSvc
Running  Sysmon64
```

Purpose of each service:

| Service | Purpose |
|---|---|
| `WazuhSvc` | Sends Windows endpoint logs to the Wazuh server |
| `Sysmon64` | Generates detailed process, network, DNS, registry, and file activity logs |

If `WazuhSvc` is not running, Wazuh will not receive logs from the Windows endpoint.

If `Sysmon64` is not running, Sysmon events such as process creation, network connections, and DNS queries will not be generated.

## Step 2 - Configure Windows Log Collection

Open the Wazuh Agent configuration file as Administrator:

```powershell
notepad "C:\Program Files (x86)\ossec-agent\ossec.conf"
```

Add or verify the following blocks inside the `<ossec_config>` section:

```xml
<localfile>
  <location>Security</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>System</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Application</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Microsoft-Windows-PowerShell/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Microsoft-Windows-TaskScheduler/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Microsoft-Windows-Windows Defender/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

![Configure](/screenshots/phase-03/configure.png)


Important log sources:

| Log Source | Purpose |
|---|---|
| `Security` | Logons, failed logons, user creation, group changes, process creation |
| `System` | Services, drivers, and system-level events |
| `Application` | Application logs |
| `Microsoft-Windows-Sysmon/Operational` | Process, network, DNS, registry, and file activity |
| `Microsoft-Windows-PowerShell/Operational` | PowerShell execution, module logs, and script block logs |
| `Microsoft-Windows-TaskScheduler/Operational` | Scheduled task activity and persistence testing |
| `Microsoft-Windows-Windows Defender/Operational` | Defender alerts, tamper attempts, and malware detections |

These blocks must be placed inside:

```xml
<ossec_config>
  ...
</ossec_config>
```

Do not place them after the closing `</ossec_config>` tag.

## Step 3 - Enable Windows Audit Policy

Audit Policy controls whether Windows records important security activity.

Run the following commands in PowerShell as Administrator:

```powershell
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"User Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Security Group Management" /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable
auditpol /set /subcategory:"Other Object Access Events" /success:enable /failure:enable
```
![Configure](/screenshots/phase-03/audit.png)

Verify the configuration:

```powershell
auditpol /get /category:*
```

Important events generated from these settings:

| Event ID | Meaning |
|---|---|
| `4624` | Successful logon |
| `4625` | Failed logon |
| `4688` | Process created |
| `4720` | New local user created |
| `4732` | User added to a local group |
| `4698` | Scheduled task created |

If Audit Policy is not enabled, the activity may happen on the endpoint but Windows may not write the event. In that case, Wazuh will have no event to collect.

## Step 4 - Enable Command-Line Logging

Enable command-line details for Windows Security Event ID `4688`:

```powershell
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f
```

This allows SOC analysts to see the full command line, not only the process name.

Examples of useful command-line visibility:

```text
powershell.exe -ExecutionPolicy Bypass
powershell.exe -EncodedCommand ...
net user soc_lab_user P@ssw0rd123! /add
schtasks /Create ...
```

## Step 5 - Enable PowerShell Logging

PowerShell logging helps detect suspicious PowerShell behavior such as encoded commands, download commands, and Defender configuration changes.

Run the following commands in PowerShell as Administrator:

```powershell
New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell" -Force | Out-Null

New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Force | Out-Null
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockLogging" -Type DWord -Value 1

New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Force | Out-Null
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Name "EnableModuleLogging" -Type DWord -Value 1

New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging\ModuleNames" -Force | Out-Null
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging\ModuleNames" -Name "*" -Type String -Value "*"
```

Verify the registry settings:

```powershell
Get-ItemProperty "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging"
Get-ItemProperty "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging"
```

Important PowerShell events:

| Event ID | Meaning |
|---|---|
| `4103` | PowerShell Module Logging |
| `4104` | PowerShell Script Block Logging |

## Step 6 - Configure File Integrity Monitoring

For this lab, monitor only a small test folder to reduce noise:

```text
C:\Users\Public\SOC-Lab
```

Create the folder:

```powershell
New-Item -ItemType Directory -Force C:\Users\Public\SOC-Lab
```

Add or update the `<syscheck>` block inside `ossec.conf`:

```xml
<syscheck>
  <disabled>no</disabled>
  <frequency>60</frequency>
  <scan_on_start>yes</scan_on_start>

  <directories check_all="yes" realtime="yes" report_changes="yes">C:\Users\Public\SOC-Lab</directories>
</syscheck>
```

Configuration meaning:

| Setting | Meaning |
|---|---|
| `<disabled>no</disabled>` | Enables FIM |
| `<frequency>60</frequency>` | Runs a periodic scan every 60 seconds during testing |
| `<scan_on_start>yes</scan_on_start>` | Builds a baseline when the agent starts |
| `check_all="yes"` | Monitors hash, size, permissions, owner, and timestamp |
| `realtime="yes"` | Watches for changes close to real time |
| `report_changes="yes"` | Reports file content changes when supported |
| `C:\Users\Public\SOC-Lab` | Safe test folder for FIM validation |

Avoid monitoring noisy paths during the first test, such as:

```text
C:\Windows
C:\Users
C:\Program Files
```

These paths change often and can generate many false positives.

## Step 7 - Restart and Validate the Agent

Restart the Wazuh Agent:

```powershell
Restart-Service WazuhSvc
```

Confirm the service is running:

```powershell
Get-Service WazuhSvc
```

Check the Wazuh Agent log:

```powershell
Get-Content "C:\Program Files (x86)\ossec-agent\ossec.log" -Tail 120
```

Filter for common configuration or FIM errors:

```powershell
Select-String -Path "C:\Program Files (x86)\ossec-agent\ossec.log" -Pattern "ERROR|Invalid|syscheck|SOC-Lab|realtime" -Context 1,2
```

Use this step to confirm that the XML is valid, event channels are readable, and the FIM folder is being monitored.

## Step 8 - Test FIM

Create, modify, and delete a unique test file:

```powershell
$testFile = "C:\Users\Public\SOC-Lab\fim-proof-$(Get-Date -Format yyyyMMdd-HHmmss).txt"

Set-Content $testFile "FIM created"
Start-Sleep -Seconds 5

Add-Content $testFile "FIM modified"
Start-Sleep -Seconds 5

Remove-Item $testFile

Write-Host "Test file was: $testFile"
```

The unique filename makes it easier to find the exact alert in Wazuh:

```text
fim-proof-YYYYMMDD-HHMMSS.txt
```

Expected FIM actions:

- file created
- file modified
- file deleted

## Step 9 - Verify in Wazuh Dashboard

Open one of the following Dashboard sections:

```text
Wazuh Dashboard -> Endpoints security -> File integrity monitoring
```

![Configure](/screenshots/phase-03/testrule.png)


We need to see the FIM alerts for the test file created, modified, and deleted in the previous step.

