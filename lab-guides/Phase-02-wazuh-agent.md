# Phase 02 - Wazuh Agent Setup on Windows

## Objective

The objective of this phase is to deploy a Wazuh agent on a Windows endpoint and enable the key telemetry sources needed for later detection scenarios.

At the end of this phase, the Windows machine should:

- appear as an active agent in the Wazuh Dashboard
- send Windows Security logs
- send PowerShell logs
- send Sysmon logs
- provide richer endpoint visibility for detection testing

## Environment

| Item | Value |
|---|---|
| Endpoint OS | Windows 10 / Windows 11 |
| Wazuh Component | Wazuh Agent |
| Wazuh Manager IP | `192.168.116.145` |
| Example Agent Name | `win11-client` |
| Required Access | PowerShell as Administrator |

## What Was Completed

In this phase, the Windows endpoint was connected to the Wazuh server and prepared to forward useful security telemetry.

Completed items:

- Wazuh agent deployed from the Dashboard-generated installer command
- Wazuh agent service started and verified
- Windows audit policies enabled
- PowerShell logging enabled
- Sysmon installed with a standard configuration
- Additional Windows Event Channels added to `ossec.conf`

## Step 1 - Deploy the Wazuh Agent

Open the Wazuh Dashboard and go to:

```text
Wazuh Dashboard -> Agents -> Deploy new agent
```

Use the following values:

- Operating system: `Windows`
- Server address: `192.168.116.145`
- Agent name: `win11-client`

The Dashboard will generate an installation command. A typical example is:

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.5-1.msi -OutFile ${env:tmp}\wazuh-agent.msi
msiexec.exe /i ${env:tmp}\wazuh-agent.msi /q WAZUH_MANAGER="192.168.116.145" WAZUH_AGENT_NAME="win11-client"
NET START WazuhSvc
```

Run the command in PowerShell with Administrator privileges on the Windows endpoint.

## Step 2 - Verify Agent Service

Check the Wazuh agent service:

```powershell
Get-Service WazuhSvc
```

Expected result:

```text
Status   Name
Running  WazuhSvc
```

Then return to the Dashboard:

```text
Wazuh Dashboard -> Agents
```

The agent should appear with status `Active`.

If the agent does not become active yet, restart the service:

```powershell
Restart-Service WazuhSvc
```

Once the agent is active, it has successfully connected to the Wazuh server and is ready to send endpoint logs.

## Step 3 - Enable Windows Audit Policy

### Purpose

This step enables Windows Security Event logging for important activity such as logons, process creation, account management, and group changes.

Common events collected from this step include:

| Event ID | Meaning |
|---|---|
| `4624` | Successful logon |
| `4625` | Failed logon |
| `4688` | Process created |
| `4720` | New user created |
| `4732` | User added to a local group |
| `4698` | Scheduled task created |

Run the following commands in PowerShell as Administrator:

```powershell
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable
auditpol /set /subcategory:"Security Group Management" /success:enable /failure:enable
```

Enable command-line logging for Event ID `4688`:

```powershell
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f
```

## Step 4 - Enable PowerShell Logging

### Purpose

This step helps detect suspicious PowerShell activity such as:

- `powershell -ExecutionPolicy Bypass`
- `powershell -EncodedCommand`
- `Invoke-WebRequest`
- `DownloadString`
- `Set-MpPreference`

Important PowerShell events:

| Event ID | Meaning |
|---|---|
| `4103` | PowerShell Module Logging |
| `4104` | PowerShell Script Block Logging |

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

## Step 5 - Install Sysmon

### Purpose

Sysmon provides deeper endpoint telemetry than the default Windows logs and is essential for many detection scenarios in this lab.

Important Sysmon events include:

| Sysmon Event ID | Meaning |
|---|---|
| `1` | Process creation |
| `3` | Network connection |
| `7` | Image loaded |
| `11` | File created |
| `12/13/14` | Registry activity |
| `22` | DNS query |

Without Sysmon, detections related to PowerShell abuse, outbound connections, and process chains will be much weaker.

Download and extract Sysmon:

```powershell
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "C:\Tools\Sysmon.zip"
Expand-Archive -Path "C:\Tools\Sysmon.zip" -DestinationPath "C:\Tools\Sysmon" -Force
```

Download a common Sysmon configuration:

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml" -OutFile "C:\Tools\Sysmon\sysmonconfig.xml"
```

Install Sysmon:

```powershell
cd C:\Tools\Sysmon
.\Sysmon64.exe -accepteula -i .\sysmonconfig.xml
```

Verify the service:

```powershell
Get-Service Sysmon64
```

Expected result:

```text
Status   Name      DisplayName
Running  Sysmon64  Sysmon64
```

## Step 6 - Add Extra Event Channels to Wazuh Agent

### Purpose

By default, the agent may not collect all Windows Event Channels needed for this lab. We need to explicitly add the channels for:

- Sysmon
- PowerShell
- Task Scheduler
- Windows Defender

Open the Wazuh agent configuration file as Administrator:

```powershell
notepad "C:\Program Files (x86)\ossec-agent\ossec.conf"
```

Inside the `<ossec_config>` section, add:

```xml
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

After saving the file, restart the Wazuh agent service:

```powershell
Restart-Service WazuhSvc
```

## Verification Checklist

Use the following checks to confirm the setup is complete:

- `Get-Service WazuhSvc` returns `Running`
- `Get-Service Sysmon64` returns `Running`
- the agent appears as `Active` in the Wazuh Dashboard
- Windows Security events are being generated
- PowerShell Operational logs are available
- Sysmon Operational logs are available

## Expected Result

After completing this phase:

- the Windows endpoint is enrolled in Wazuh
- the agent is visible and active in the Dashboard
- Windows audit logs are available for detection
- PowerShell activity is logged with better visibility
- Sysmon telemetry is collected for richer endpoint monitoring

This phase prepares the endpoint for the next steps in the lab, where we will validate detections and analyze security events from the Windows host.

## Notes

- Screenshots have not been added yet.
- If needed, screenshots can be inserted later for:
  - agent deployment from Dashboard
  - agent status `Active`
  - Sysmon service status
  - `ossec.conf` event channel configuration
