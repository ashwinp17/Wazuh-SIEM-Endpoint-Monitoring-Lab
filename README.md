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

## Threat Detection & Findings

To validate that this Wazuh deployment does more than passively collect logs, four detection scenarios were run against the monitored Windows endpoint, covering authentication abuse, file integrity, malware/hacktool arrival, and security-service state changes.

| # | Scenario | Trigger Action | Rule ID | Rule Description | Level |
|---|----------|-----------------|---------|-------------------|-------|
| 1 | Brute-force logon | Multiple failed local logon attempts | 60122 | Logon Failure - Unknown user or bad password | 5 |
| 2 | Unauthorized file change (FIM) | Created/modified a file in a monitored directory | 550 | Integrity checksum changed | 7 |
| 3 | Malware/hacktool arrival | Downloaded the EICAR industry-standard AV test file | 554 | File added to the system | 5 |
| 4 | Security control state change | Toggled AV real-time protection off/on | 60642 | Software protection service scheduled successfully | 3 |

### Scenario Details

**1. Brute-Force Logon Detection**
Deliberately entered an incorrect password multiple times at the Windows lock screen. Wazuh flagged each attempt under the `authentication_failed` rule group. In a live environment, an analyst would check whether the source was local or remote, correlate against a failure-count threshold, and escalate if attempts originated from an unrecognized host or account.

**2. File Integrity Monitoring (FIM)**
Configured a monitored directory in `ossec.conf` using `check_all` and `report_changes`. Initial testing showed no alerts because syscheck's default scan interval is 12 hours; adding `realtime="yes"` enabled near-instant detection. Editing a file inside the monitored path triggered a "checksum changed" alert within seconds — a directly relevant skill, since FIM is the same mechanism that would flag a ransomware payload or webshell drop in a production environment.

**3. Malware/Hacktool Detection (EICAR Test File)**
Downloaded the EICAR test file, a harmless string recognized industry-wide as a benign malware-detection test. The endpoint's antivirus (McAfee) blocked/quarantined it locally. Wazuh's FIM captured the file's arrival on disk (rule 554) before McAfee's real-time engine acted on it.

*Note: McAfee does not forward its own detection/quarantine logs to Wazuh by default in this lab setup — that would require a custom log source/decoder integration, a natural next step for this project. This is a realistic and common gap in SOC environments where not every third-party security tool ships logs to the SIEM out of the box.*

**4. Security Control State Change**
Toggled McAfee's real-time scanning off, then back on. Wazuh picked up the resulting service-level state changes (rule 60642), reflecting the security software rescheduling its protection tasks. This models a "defense evasion" pattern — an attacker disabling AV/EDR is a common early step in an intrusion, and having visibility into security-control state changes is a key SOC detection use case.

### Key Takeaway
This phase demonstrated that the Wazuh deployment isn't just collecting logs passively — it can surface authentication abuse, file tampering, malware arrival, and security-control state changes, the categories of events a SOC analyst is expected to triage daily. It also surfaced a real gap (McAfee log forwarding) worth addressing in future iterations of this lab.

### Screenshots

   **Brute-Force Logon Detection**
   ![Brute force logon](screenshots/01-bruteforce-logon-failure.png)

   **FIM - Checksum Changed**
   ![FIM alert](screenshots/02-fim-checksum-changed.png)

   **EICAR File Detected**
   ![EICAR detection](screenshots/03-eicar-file-detected.png)

   **Security Control State Change**
   ![Security state change](screenshots/04-security-control-state-change.png)
