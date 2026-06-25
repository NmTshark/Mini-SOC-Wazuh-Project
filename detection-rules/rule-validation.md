# Rule Validation

## Deployment

Back up the manager rule file:

```bash
sudo cp /var/ossec/etc/rules/local_rules.xml \
  /var/ossec/etc/rules/local_rules.xml.bak
```

Copy the project rules to `/var/ossec/etc/rules/local_rules.xml`, validate them, and restart only after successful testing:

```bash
sudo /var/ossec/bin/wazuh-logtest
sudo systemctl restart wazuh-manager
sudo systemctl status wazuh-manager
```

## Using `wazuh-logtest`

`wazuh-logtest` accepts one raw event at a time and shows decoder and rule phases. For Windows events, use a representative JSON event copied from Wazuh archives or the Dashboard rather than inventing fields.

Expected output should identify:

- the decoder
- the base Sysmon or FIM rule/group
- the custom rule ID
- alert level
- description
- MITRE mapping where configured

Example validation targets:

| Scenario | Expected custom rule |
|---|---:|
| FIM file added under `C:\SOC-Lab` | `100100` |
| Sysmon Event ID 11 under `C:\SOC-Lab` | `100110` |
| Sysmon Event ID 15 named stream | `100115` |
| Monitored PowerShell process options | `100120` |

## Endpoint tests

```powershell
New-Item -ItemType Directory -Force "C:\SOC-Lab" | Out-Null

Set-Content "C:\SOC-Lab\rule-file-test.txt" "SOC-LAB"

Set-Content "C:\SOC-Lab\ads-host.txt" "visible"
Set-Content "C:\SOC-Lab\ads-host.txt:soc-lab-stream" "stream"

powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB rule test'"
```

## Dashboard validation

Search:

```text
rule.id:(100100 OR 100110 OR 100115 OR 100120)
```

Inspect:

- `rule.id`
- `rule.level`
- `rule.description`
- `rule.mitre.id`
- `agent.name`
- `data.win.system.eventID`
- `data.win.eventdata.image`
- `data.win.eventdata.commandLine`
- `data.win.eventdata.targetFilename`
- `syscheck.path`
- `syscheck.event`

## Pass criteria

A rule passes when:

1. The intended endpoint event exists.
2. Wazuh collects and decodes the expected fields.
3. The intended custom rule matches.
4. The alert is visible in the Dashboard.
5. Unrelated control activity does not match unexpectedly.

## Cleanup

```powershell
Remove-Item "C:\SOC-Lab\rule-file-test.txt" -Force -ErrorAction SilentlyContinue
Remove-Item "C:\SOC-Lab\ads-host.txt" -Force -ErrorAction SilentlyContinue
```

If a rule fails, compare the actual decoded field names with the rule conditions before changing severity or adding broader patterns.
