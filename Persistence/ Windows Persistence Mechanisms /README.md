# Windows Persistence Mechanisms and Threat Hunting

## Overview

This project focuses on identifying and investigating Windows persistence mechanisms that allow applications or processes to execute automatically during system startup or user logon.

Two investigation scenarios were performed to understand how Windows Registry Run Keys and Scheduled Tasks can be used for persistence.

Controlled persistence simulations using a safe Notepad application were performed to understand how legitimate Windows mechanisms can potentially be abused by attackers to maintain persistence.

## Detection Scenarios

### Hunt 1: Registry Run Key Investigation

- Identify applications configured to execute automatically during user logon.
- Examine Windows Registry Run Key entries.
- Investigate executable paths and startup arguments.
- Identify potentially suspicious or unauthorized startup entries.

### Hunt 2: Scheduled Tasks Investigation

- Enumerate Windows Scheduled Tasks.
- Examine task actions, executable paths, and command-line arguments.
- Understand how scheduled tasks can be abused to maintain persistence.
- Perform a controlled persistence simulation using the Notepad application.

## Tools and Telemetry

| Component | Purpose |
|---|---|
| Windows PowerShell | Registry and scheduled task investigation |
| Windows Registry | Inspect startup persistence entries |
| Windows Task Scheduler | Investigate scheduled task persistence |
| Windows Event Viewer | Review relevant system and security events |
| Sysmon | Additional endpoint telemetry |

# Hunt 1: Registry Run Key Investigation

## 1. Objective

Identify applications configured to execute automatically when a user logs in to Windows and investigate potentially suspicious startup entries.

## 2. Test Activity

Inspected the current user's Windows Registry Run Key to identify configured startup applications.

```powershell
Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```

## 3. Data Source

**Windows Registry – Current User Run Key**

Registry path:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```
<img width="1024" height="768" alt="Screenshot (517)" src="https://github.com/user-attachments/assets/0f94dbcd-e1a4-489e-b44b-387d2ad1be57" />




This registry location stores application entries configured to launch automatically when the current user signs in.

We can also cross check whether the new item i.e SOC Lab 5 have been created using Event viewer as per above screenshots

## 4. Command Explanation

- `Get-ItemProperty` retrieves values stored in a registry key.
- `HKCU` represents `HKEY_CURRENT_USER`.
- `Software\Microsoft\Windows\CurrentVersion\Run` contains startup entries for the current user.
- These entries can automatically launch applications during user logon.


## 5. SOC Investigation

A SOC analyst should examine:

1. Whether the application is expected.
2. Whether the executable path is legitimate.
3. Whether the file is digitally signed.
4. Whether the registry entry was recently created or modified.
5. Whether the application contains suspicious execution arguments.

## 6. Investigation Considerations

Unknown executables, unusual file paths, suspicious command-line arguments, and unexpected registry modifications should be investigated further.

## 7. Conclusion

The registry investigation provided practical experience identifying applications configured for automatic execution during user logon. It also demonstrated how SOC analysts can inspect startup entries to identify potential persistence indicators.

# Hunt 2: Scheduled Tasks Investigation

## 1. Objective

Identify scheduled tasks configured to automatically execute programs or scripts based on predefined triggers.

Understand how attackers can abuse Windows Task Scheduler to maintain persistence after system restarts or user logons.

## 2. Test Activity

A controlled persistence simulation was performed using the Windows Notepad application and Scheduled Tasks to understand task-based execution.

## 3. Data Source

**Windows Task Scheduler**

Relevant information includes:

- Task Name
- Task Path
- Task State
- Executable Path
- Command-line Arguments
- Configured Triggers
- Execution Conditions

## 4. Hunting Commands

The following commands can be used to investigate scheduled tasks.

### Command 1: List Scheduled Tasks

```powershell
Get-ScheduledTask |
Select-Object TaskName, TaskPath, State
```

**Command Explanation**

- `Get-ScheduledTask` retrieves scheduled tasks configured on the system.
- `TaskName` displays the task name.
- `TaskPath` displays the task folder location.
- `State` displays the current task state.

### Command 2: Examine Scheduled Task Actions

```powershell
Get-ScheduledTask | ForEach-Object {
    $task = $_

    foreach ($action in $task.Actions) {
        [PSCustomObject]@{
            TaskName  = $task.TaskName
            TaskPath  = $task.TaskPath
            Execute   = $action.Execute
            Arguments = $action.Arguments
        }
    }
} | Format-Table -AutoSize
```

**Command Explanation**

- Retrieves scheduled tasks from Windows.
- Iterates through each task and its configured actions.
- Extracts the executable associated with each task.
- Displays command-line arguments used during execution.
- Helps identify tasks launching PowerShell, CMD, scripts, or unfamiliar executables.

## 5. Detection Logic

Investigate scheduled tasks that exhibit potentially suspicious characteristics:

- Tasks with unfamiliar names.
- Executables running from Temp or AppData directories.
- Tasks launching PowerShell or CMD with suspicious arguments.
- Tasks configured to execute during startup or user logon.
- Unexpected task creation or modification.

## 6. SOC Investigation

1. Identify unfamiliar scheduled tasks.
2. Examine task actions and executable paths.
3. Review configured triggers and execution conditions.
4. Validate the executable and its digital signature.
5. Identify the account associated with task execution.
6. Correlate task activity with available Windows event logs.

## 7. Evidence to Collect

- Scheduled task enumeration.
- Task action and executable path.
- Task properties and configured triggers.
- Relevant task creation or execution events.

# Persistence Detection and Analysis

## 1. Objective

Correlate findings from Registry Run Keys and Scheduled Tasks to identify potential Windows persistence mechanisms.

## 2. Investigation Areas

| Investigation | What We Examine | Potential Indicator |
|---|---|---|
| Registry Run Keys | Startup registry entries | Unknown executable or suspicious path |
| Scheduled Tasks | Task triggers and actions | Unexpected script or executable |

## 3. SOC Investigation Workflow

1. Identify an unfamiliar startup entry or scheduled task.
2. Examine its executable path.
3. Validate the file, publisher, and digital signature.
4. Review execution arguments and associated user account.
5. Check relevant creation or modification timestamps.
6. Correlate available Windows logs and endpoint telemetry.
7. Determine whether the activity is legitimate, suspicious, or confirmed malicious.

## 4. Important Event IDs for Investigation

| Event ID | Log Source | Investigation Purpose |
|---|---|---|
| 4657 | Windows Security | Registry value modification, when appropriate auditing is enabled |
| 106 | Task Scheduler Operational | Scheduled task registration |
| 110 | Task Scheduler Operational | Scheduled task triggered |

Event availability depends on the configured logging and auditing policies.

## 5. Conclusion

This lab focuses on understanding Windows persistence mechanisms through Registry Run Keys and Scheduled Tasks.

The Registry Run Key inspection identified existing OneDrive and Microsoft Edge startup entries. A controlled persistence simulation was performed using the safe Notepad application and Scheduled Tasks.

These activities helped develop practical knowledge of Windows persistence mechanisms and how SOC analysts can investigate suspicious startup entries and scheduled task configurations to identify potential persistence indicators.
