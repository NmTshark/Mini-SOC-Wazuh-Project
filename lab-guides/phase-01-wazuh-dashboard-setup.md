# Phase 01 - Wazuh Dashboard/Admin Setup

## Objective

The objective of this phase is to install and access the Wazuh Dashboard/Admin interface.

This dashboard will be used as the main interface for monitoring agents, reviewing alerts, validating detection scenarios, and analyzing security events in later phases.

## Environment

| Item | Value |
|---|---|
| Server OS | Ubuntu Server |
| SIEM Platform | Wazuh |
| Wazuh Components | Wazuh Manager, Wazuh Indexer, Wazuh Dashboard |
| Access Method | Web Browser |

## What Was Completed

In this phase, the Wazuh server components were installed and the Wazuh Dashboard/Admin interface was successfully accessed from a browser.

Completed items:

- Ubuntu server prepared
- Wazuh components installed
- Wazuh Dashboard accessed from browser
- Wazuh admin login verified
- Dashboard interface confirmed working

## Download and run the Wazuh installation assistant

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

The `-a` option installs the Wazuh central components in an all-in-one deployment.

This includes:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

After the installation completes, the Wazuh Dashboard should be accessible from a browser using the Wazuh server IP address and the default admin credentials will be provided in the installation output.
## Verification Commands

The following commands can be used on the Wazuh server to verify service status:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

The Wazuh server IP address can be checked with:

```bash
ip a
```


## Expected Result

The Wazuh Dashboard should be reachable from a browser using the Wazuh server IP address.

Example:

```text
https://<WAZUH_SERVER_IP>
```
![Wazuh Dashboard](/screenshots/phase-01/dashboard-wazuh.png)

The admin account should be able to log in successfully.

After logging in, the Wazuh Dashboard interface should load without errors, allowing access to the various sections for monitoring and analysis.

![Wazuh Dashboard Home](/screenshots/phase-01/home-wazuh.png)

The Wazuh Dashboard/Admin interface was successfully installed and accessed from the browser. Going to this step was crucial to ensure that the central SIEM platform is operational before proceeding with agent setup and log collection in later phases and confirming that the Wazuh Dashboard is working properly will allow for effective monitoring and analysis of security events as we progress through the lab.


