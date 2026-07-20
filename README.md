# Cybersecurity Lab — Wazuh SIEM & Endpoint Monitoring

## Project Overview

Deployed and configured a Wazuh security monitoring lab using the official Wazuh OVA in Oracle VirtualBox. The lab focused on setting up a working SIEM environment, connecting a Windows endpoint as an agent, validating agent communication, and confirming that Wazuh was collecting security events from the monitored system.

This project helped demonstrate endpoint visibility, centralized security monitoring, agent deployment, service validation, and troubleshooting of network connectivity between a Windows host and a Wazuh server.

> This lab was completed in a controlled educational environment. No real organizational systems, unauthorized devices, or production networks were monitored.

---

## Lab Environment

| Component | Details |
|---|---|
| SIEM Platform | Wazuh v4.14.6 OVA |
| Virtualization | Oracle VirtualBox |
| Wazuh Server | Wazuh Manager, Indexer, and Dashboard |
| Monitored Endpoint | Windows 11 Home |
| Agent Name | Windows-Laptop |
| Connection Method | VirtualBox NAT with Port Forwarding |
| Dashboard URL | `https://127.0.0.1:8443` |

---

## Tools and Technologies Used

- Wazuh SIEM
- Wazuh Agent
- Wazuh Dashboard
- Oracle VirtualBox
- Windows PowerShell
- Windows Services
- NAT Port Forwarding
- TCP Connectivity Testing
- Endpoint Monitoring
- Threat Hunting Dashboard

---

## Objectives

- Deploy a working Wazuh SIEM lab environment
- Access the Wazuh dashboard from the Windows host
- Install and configure the Wazuh agent on Windows
- Validate agent communication with the Wazuh server
- Confirm the Windows endpoint appears as an active agent
- Review collected security events in the Wazuh Threat Hunting dashboard
- Troubleshoot common SIEM deployment and connectivity issues

---

## Network and Port Forwarding Configuration

Because the Wazuh server was running inside VirtualBox using NAT networking, port forwarding was required for the Windows host to communicate with the Wazuh VM.

The following port forwarding rules were configured:

| Purpose | Host Port | Guest Port |
|---|---:|---:|
| Wazuh Dashboard | 8443 | 443 |
| Wazuh Agent Communication | 1514 | 1514 |
| Wazuh Agent Registration | 1515 | 1515 |

The Wazuh dashboard was accessed from the Windows host using:

```text
https://127.0.0.1:8443
