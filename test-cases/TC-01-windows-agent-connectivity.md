# TC-01 - Windows Agent Connectivity

## Test case ID

`TC-01`

## Objective

Confirm that the Windows Wazuh agent service is running and the endpoint is active in the Dashboard.

## Commands

```powershell
Get-Service WazuhSvc
Get-Content "C:\Program Files (x86)\ossec-agent\ossec.log" -Tail 50
```

## Expected Windows evidence

- `WazuhSvc` status is `Running`.
- The agent log shows successful manager communication.
- No persistent authentication or configuration error is present.

## Expected Wazuh evidence

- Agent status is `Active`.
- The last-keepalive time is current.
- The displayed agent name and operating system match the test endpoint.

## Screenshot checklist

- [ ] Windows service status
- [ ] Agent log with sensitive values redacted
- [ ] Dashboard agent summary

## Pass/fail criteria

- **Pass:** Service is running and the Dashboard reports the expected agent as active.
- **Fail:** Service is stopped, communication errors persist, or the agent is disconnected.

