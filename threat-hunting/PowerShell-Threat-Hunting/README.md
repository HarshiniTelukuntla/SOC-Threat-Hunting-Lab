# Lab 3: PowerShell Download Detection

## 1. Objective

Detect and investigate PowerShell activity associated with downloading content from external URLs. Identify potentially suspicious commands, validate the resulting file activity, and distinguish legitimate administrative activity from potentially malicious behavior.

## 2. Lab Environment

| Component | Purpose |
|---|---|
| Windows 11 VM | Investigation environment |
| Windows PowerShell | Command execution and hunting |
| Windows Event Viewer | Event investigation |
| PowerShell Operational Log | Script-block telemetry |
| Windows Security Log | Process creation telemetry |
| Sysmon | Process and network telemetry |
| Splunk Universal Forwarder | Log collection |

## 3. Data Sources

### Event ID 4104 — PowerShell Script Block Logging

Used to investigate PowerShell script content, including commands that may involve downloading files or accessing external URLs.

### Event ID 4688 — Process Creation

Used to identify newly created processes, their parent processes, and available command-line information.

### Sysmon Event ID 3 — Network Connection

Intended to correlate process activity with network connections. A matching network event for the test download has not yet been confirmed.

## 4. Detection Indicators

The following command patterns are useful hunting indicators:

- `Invoke-WebRequest`
- `Invoke-RestMethod`
- `DownloadString`
- `DownloadFile`
- `Start-BitsTransfer`
- `curl.exe`
- `wget`

These indicators are not inherently malicious. Their presence should trigger investigation and contextual validation rather than an automatic malicious verdict.

## 5. Test Activity Performed

A controlled download test was performed using PowerShell:

```powershell
$testFile = Join-Path $env:TEMP 'SOC-Download-Hunt-Test.html'

Invoke-WebRequest -Uri 'https://example.com' -OutFile $testFile

Get-Item $testFile | Select-Object FullName, Length, LastWriteTime
```

### Test results

- The web request command was executed.
- The output file was created in the user's temporary directory.
- The file's full path, length, and last-write time were displayed.
- PowerShell Event ID 4104 contained records associated with the test commands.
- Splunk-related PowerShell activity was also present in the investigation data and must be distinguished from the test activity.

## 6. Hunting Logic

Search PowerShell Event ID 4104 for download-related indicators.

```powershell
$since = (Get-Date).AddHours(-2)

Get-WinEvent -FilterHashtable @{
    LogName   = 'Microsoft-Windows-PowerShell/Operational'
    Id        = 4104
    StartTime = $since
} -ErrorAction SilentlyContinue |
Where-Object {
    $_.Message -match '(?i)Invoke-WebRequest|DownloadString|DownloadFile|Start-BitsTransfer|Invoke-RestMethod|curl\.exe|wget'
} |
Select-Object TimeCreated, RecordId, Message |
Format-List
```

### Logic explanation

1. Retrieve PowerShell script-block events from the last two hours.
2. Filter for common download-related commands and indicators.
3. Display the timestamp, event record ID, and event message.
4. Investigate the command, user, parent process, destination URL, and resulting file where available.
5. Correlate the activity with process-creation and network telemetry when possible.


## 7. Investigation Workflow

When a download-related indicator is found:

1. Identify the user and process involved.
2. Examine the complete PowerShell command and its arguments.
3. Identify the URL or remote destination, if recorded.
4. Determine whether a file was created and inspect its path, size, and timestamp.
5. Review process-creation telemetry, including Event ID 4688.
6. Correlate with Sysmon Event ID 3 or other available network telemetry.
7. Check whether the activity aligns with an approved administrative task.
8. Record the findings and determine whether escalation is warranted.

## 8. False-Positive Considerations

Legitimate activities can produce the same indicators, including:

- Software downloads and updates.
- Administrator troubleshooting.
- Approved automation scripts.
- Application installation and configuration.
- Security-tool and monitoring-agent operations.

Splunk Universal Forwarder activity was observed during this lab. Such activity should be evaluated using its process path, parent process, command line, and context rather than excluded solely because it contains the word `Splunk`.

## 9. Evidence and Limitations

### Confirmed observations

- PowerShell download-related test commands were executed.
- Event ID 4104 records associated with the test commands were found.
- A temporary HTML file was created successfully.
- Security Event ID 4688 previously showed PowerShell launching an encoded PowerShell command during a separate controlled test.


## 10. MITRE ATT&CK Mapping

**Potential mapping:** [T1059.001 — PowerShell](https://attack.mitre.org/techniques/T1059/001/)

PowerShell is frequently used for legitimate administration and can also be abused by adversaries. Map the observed behavior to this technique when the evidence supports the association.



## 11. Conclusion

This lab demonstrates how PowerShell script-block logging can support hunting for download-related behavior. The investigation identified the controlled test commands and confirmed output-file creation.

