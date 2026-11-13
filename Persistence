Lab 2 
Windows Persistence Mechanisms and Threat Hunting

Overview
This lab focuses on understanding and investigating Windows persistence mechanisms that allow applications or processes to execute automatically. The objective is to identify suspicious startup activity, simulate persistence using a safe Notepad application, and understand how attackers may abuse legitimate Windows mechanisms to maintain persistence.

1. Registry Run Keys Investigation

Objective: Identify applications configured to execute automatically when a user logs in to Windows.

Command used
Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"


Explanation
- Get-ItemProperty retrieves the values stored in a registry key.
- HKCU represents HKEY_CURRENT_USER.
- Software\Microsoft\Windows\CurrentVersion\Run contains startup entries configured for the current user.
- These entries can automatically launch applications when the user signs in.
\

SOC investigation
A SOC analyst should examine:
1. Whether the application is expected.
2. Whether the executable path is legitimate.
3. Whether the file is digitally signed.
4. Whether the registry entry was recently created or modified.
5. Whether the application has suspicious execution arguments.


2. Scheduled Tasks Investigation
Objective: Identify scheduled tasks that automatically execute programs or scripts based on configured triggers.

Scheduled Tasks can be abused by attackers to maintain persistence after a system restart or user logon.
Commands to perform
I don't have a screenshot showing that we executed these commands yet. Use the following for this investigation.

Command 1 – List scheduled tasks
Get-ScheduledTask |
Select-Object TaskName, TaskPath, State


What it does:
- Retrieves scheduled tasks from Windows.
- Displays task names, their folder paths, and current states.
- Helps identify unfamiliar or unexpected tasks.
Command 2 – Examine task actions
Get-ScheduledTask | ForEach-Object {
    $task = $_

    foreach ($action in $task.Actions) {
        [PSCustomObject]@{
            TaskName = $task.TaskName
            TaskPath = $task.TaskPath
            Execute  = $action.Execute
            Arguments = $action.Arguments
        }
    }
} | Format-Table -AutoSize


What it does:
- Extracts the executable associated with each task.
- Displays command-line arguments.
- Helps identify tasks launching PowerShell, CMD, scripts, or unusual executables.

SOC investigation
Pay attention to:
- Tasks with unfamiliar names.
- Tasks executing from Temp or AppData directories.
- Tasks launching PowerShell or CMD with suspicious arguments.
- Tasks configured to run at startup or logon.
- Unexpected task creation or modification.


After executing the commands, capture:
1. Scheduled Task enumeration.
2. Task action and executable path.
3. Properties of any suspicious task, if identified.



Persistence Detection and Analysis
This is where we correlate the information collected from all  investigations.
Investigation	What we examine	Potential indicator
Registry Run Keys	Startup registry entries	Unknown executable or suspicious path
Scheduled Tasks	Task triggers and actions	Unexpected script or executable
Windows Services	Service configuration and executable paths	Unusual service or suspicious binary

SOC investigation workflow
1. Identify an unfamiliar startup entry, scheduled task, or service.
2. Examine its executable path.
3. Validate the file, publisher, and digital signature.
4. Review execution arguments and associated user account.
5. Check relevant creation or modification timestamps.
6. Correlate available Windows logs and endpoint telemetry.
7. Determine whether the activity is legitimate, suspicious, or confirmed malicious.
Conclusion
This lab focuses on understanding Windows persistence mechanisms through Registry Run Keys, Scheduled Tasks, and Windows Services.

The Registry Run Key inspection identified existing OneDrive and Microsoft Edge startup entries. Service validation confirmed that Windows Event Log, Task Scheduler, and Sysmon64 were running with Automatic startup configuration.

The next stage is to investigate Scheduled Tasks and examine service executable paths, followed by correlating the collected evidence to identify potential persistence indicators.
