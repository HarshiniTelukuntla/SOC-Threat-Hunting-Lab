# PowerShell Threat Detection

## Overview

This project focuses on identifying suspicious PowerShell execution patterns through Windows event logs and endpoint telemetry.

Two controlled hunting scenarios were performed to understand how PowerShell activity can be detected and investigated.

## Detection Scenarios

### Hunt 1: Encoded PowerShell Command Detection

- Identify PowerShell processes using `-EncodedCommand`.
- Investigate process creation through Windows Security Event ID 4688.
- Examine command-line arguments and process context.
- Understand potential misuse of encoded PowerShell commands.

### Hunt 2: PowerShell Download Activity Detection

- Identify download-related PowerShell commands.
- Investigate script-block activity using Event ID 4104.
- Verify downloaded file creation.


## Tools and Telemetry

| Component | Purpose |
|---|---|
| Windows PowerShell | Controlled test execution |
| Windows Security Event ID 4688 | Process creation |
| PowerShell Event ID 4104 | Script-block logging |
| Sysmon | Additional endpoint telemetry |
| Splunk Universal Forwarder | Log collection |


## Important Note

The activities performed were controlled and harmless tests. The observed command execution does not independently establish malicious activity.


# Hunt 1: Encoded PowerShell Detection

## 1. Objective

Detect PowerShell execution involving the `-EncodedCommand` parameter and investigate the process using Windows Security Event ID 4688.

## 2. Test Activity

A harmless PowerShell command was created:

```powershell
$testCommand = "Write-Output 'SOC-ENCODED-HUNT-TEST'"
```

The command was converted into Base64:

```powershell
$bytes = [System.Text.Encoding]::Unicode.GetBytes($testCommand)
$encoded = [Convert]::ToBase64String($bytes)

"Encoded test: $encoded"
```

The encoded command was executed:

```powershell
Start-Process powershell.exe -Wait -ArgumentList @(
    '-NoProfile',
    '-EncodedCommand',
    $encoded
)
```

## 3. Data Source

**Windows Security Event ID 4688 — Process Creation**

Relevant fields:

- New Process Name
- New Process ID
- Creator Process Name
- Creator Process ID
- Process Command Line
- Account Name
- Logon ID

## 4. Hunting Query

```powershell
$since = (Get-Date).AddMinutes(-10)

Get-WinEvent -FilterHashtable @{
    LogName   = 'Security'
    Id        = 4688
    StartTime = $since
} -ErrorAction SilentlyContinue |
Where-Object {
    $_.Message -match '(?i)-EncodedCommand'
} |
Select-Object TimeCreated, RecordId, Message |
Format-List
```

## 5. Detection Logic

Identify process creation events where:

- The new process is `powershell.exe` or `pwsh.exe`.
- The command line contains `-EncodedCommand` or its supported abbreviation.
- The execution context and parent process require investigation.

The presence of an encoded command is a hunting indicator, not proof of malicious activity.

## 6. Evidence Collected

- Generated a harmless Base64-encoded PowerShell command.
- Executed PowerShell using `-EncodedCommand`.
- Retrieved Windows Security Event ID 4688.
- Observed the PowerShell executable and encoded command-line arguments in the event.


## 7. Investigation Steps

1. Identify the account that executed PowerShell.
2. Review the parent and child process information.
3. Examine the complete command line.
4. Decode the Base64 content using the appropriate encoding.
5. Inspect the decoded command for suspicious operations.
6. Correlate with Event ID 4104 and other endpoint telemetry when available.

## 8. False Positives

Legitimate applications and administrators may use encoded PowerShell commands for:

- Software deployment.
- Administrative automation.
- Configuration management.
- Application installation.
- Monitoring operations.

Validate the parent process, user, decoded command, and surrounding activity.

## 9. MITRE ATT&CK

**T1059.001 — PowerShell**

Potential mapping based on the observed PowerShell execution behavior.

## 10. Conclusion
In this hunt, we performed controlled PowerShell execution using an encoded command and investigated the resulting activity through Windows PowerShell and Windows Event Viewer.

We used PowerShell to generate and execute a Base64-encoded command, then examined Windows Security Event ID 4688 to identify the newly created PowerShell process and its command-line arguments.

This practical exercise helped us understand how SOC analysts can use PowerShell for threat hunting and Windows Event Viewer for validating process execution activity. It also demonstrated how encoded PowerShell commands can be identified and investigated as potential security indicators.
