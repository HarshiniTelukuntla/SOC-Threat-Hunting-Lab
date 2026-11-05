# SOC-Threat-Hunting-Lab


## About This Repository

Welcome to my **SOC Threat Hunting Lab**!

This repository documents my hands-on cybersecurity learning and practical investigations focused on Security Operations Center (SOC), Threat Hunting, Incident Investigation, and Detection Engineering.

The objective is to simulate security-related activities in a controlled Windows environment, investigate the generated telemetry, identify suspicious behavior, and develop detection logic using different security tools.

## Objectives

- Develop practical threat hunting and investigation skills.
- Understand Windows security events and endpoint telemetry.
- Investigate suspicious processes, commands, and system activities.
- Learn attacker techniques using the MITRE ATT&CK framework.
- Create and validate detection logic.
- Improve incident analysis and response capabilities.
- Document practical findings with screenshots and investigation reports.

## Lab Environment

| Component | Technology |
|---|---|
| Operating System | Windows 11 |
| Virtualization | Oracle VirtualBox |
| Scripting | PowerShell |
| Endpoint Monitoring | Sysmon |
| Log Investigation | Windows Event Viewer |
| SIEM | Splunk |
| Network Analysis | Wireshark (planned) |

## Repository Structure

```text
SOC-Threat-Hunting-Lab/
│
├── threat-hunting/
│   └── Practical threat hunting investigations
│
└── README.md
```

## Practical Investigations

### Lab 1 – PowerShell Threat Hunting and Detection
- Encoded PowerShell command investigation.
- PowerShell download activity investigation.
- Windows Security Event ID 4688 analysis.
- PowerShell Operational Event ID 4104 analysis.
- Downloaded file verification.

### Lab 2 – Windows Persistence Mechanisms and Threat Hunting
- Registry Run Keys investigation.
- Scheduled Tasks investigation.
- Windows Services investigation.
- Persistence detection and analysis.

*Lab 2 is currently in progress.*

## Investigation Methodology

Each investigation follows a structured approach:

1. Understand the attack technique.
2. Generate controlled test activity.
3. Collect endpoint and system telemetry.
4. Investigate relevant security events.
5. Identify suspicious behavior and validate findings.
6. Develop detection logic.
7. Document results and supporting evidence.

## Disclaimer

All activities in this repository are performed in a controlled lab environment for educational and defensive cybersecurity purposes. No unauthorized systems are targeted.

## Goal

To develop practical skills in SOC operations, Threat Hunting, Incident Response, and Detection Engineering through hands-on investigations and continuous learning.

