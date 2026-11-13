# Windows Persistence Mechanisms and Threat Hunting

## Overview

This project focuses on identifying and investigating Windows persistence mechanisms that allow applications to execute automatically during system startup or user logon.

Two controlled persistence scenarios were performed using Windows Registry Run Keys and Scheduled Tasks. A safe Notepad application was configured to execute automatically, and its execution was validated after restarting the system.

The objective was to understand how legitimate Windows mechanisms can be abused by attackers to maintain persistence and how SOC analysts can investigate such activities.

## Detection Scenarios

### Hunt 1: Registry Run Key Persistence

- Inspected Windows Registry Run Keys to identify existing startup applications.
- Examined application paths and startup configurations.
- Created a controlled persistence entry using the safe Notepad application.
- Successfully executed Notepad through the configured startup mechanism.
- Restarted the system and verified that Notepad opened automatically.

### Hunt 2: Scheduled Task Persistence

- Investigated Windows Scheduled Tasks and their execution mechanisms.
- Created a scheduled task configured to launch the safe Notepad application.
- Successfully executed Notepad through the scheduled task.
- Restarted the system and verified the automatic execution behavior.
- Understood how scheduled tasks can be abused to maintain persistence.

## Tools and Telemetry

| Component | Purpose |
|---|---|
| Windows PowerShell | Registry inspection and scheduled task investigation |
| Windows Registry | Configure and investigate startup persistence |
| Windows Task Scheduler | Create and investigate scheduled task persistence |
| Windows Notepad | Safe application used for persistence simulation |
| Windows Event Viewer | Investigate relevant Windows events |
| Sysmon | Additional endpoint telemetry |

# Hunt 1: Registry Run Key Persistence

## 1. Objective

Identify applications configured to execute automatically during user logon and understand how Registry Run Keys can be used to establish persistence.

## 2. Initial Investigation

Inspected the current user's Windows Registry Run Key to identify existing startup applications.

```powershell
Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```

## 3. Data Source

**Windows Registry – Current User Run Key**

Registry path:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

This registry location stores application entries configured to launch automatically when the current user signs in.

## 4. Command Explanation

- `Get-ItemProperty` retrieves values stored in a registry key.
- `HKCU` represents `HKEY_CURRENT_USER`.
- `Software\Microsoft\Windows\CurrentVersion\Run` contains startup entries for the current user.
- Applications configured in this location can automatically launch during user logon.

## 5. Initial Findings

The registry inspection identified existing startup entries associated with:

- Microsoft OneDrive.
- Microsoft Edge.

These were observed as existing startup configurations. Their presence alone does not indicate malicious activity.

## 6. Controlled Persistence Simulation

To understand Registry Run Key persistence, a safe Notepad application was used for testing.

Activities performed:

1. Created a controlled Registry Run Key entry for Notepad.
2. Configured the application to execute automatically during user logon.
3. Successfully executed Notepad using the configured startup mechanism.
4. Shut down and restarted the system.
5. Verified that Notepad opened automatically after the system restarted and the user logged in.

## 7. Detection and Investigation

A SOC analyst should investigate:

1. Whether the registry entry is expected.
2. Whether the executable path is legitimate.
3. Whether the file is digitally signed.
4. Whether the registry entry was recently created or modified.
5. Whether the application contains suspicious execution arguments.
6. Whether the startup behavior is associated with an authorized user or application.

## 8. Conclusion

This investigation demonstrated how Windows Registry Run Keys can be used to establish persistence. A controlled Notepad startup entry was created and successfully validated after a system restart, demonstrating automatic application execution during user logon.

# Hunt 2: Scheduled Task Persistence

## 1. Objective

Understand how Windows Scheduled Tasks can be configured to execute applications automatically based on predefined triggers and how attackers can abuse this mechanism to maintain persistence.

## 2. Controlled Persistence Simulation

A safe Notepad application was used to simulate scheduled task-based persistence.

Activities performed:

1. Created a scheduled task configured to launch Notepad.
2. Configured the task with the required execution trigger.
3. Successfully executed Notepad through the scheduled task.
4. Shut down and restarted the system.
5. Verified the automatic execution behavior after restarting the system.

## 3. Data Source

**Windows Task Scheduler**

Relevant task information includes:

- Task Name
- Task Path
- Task State
- Executable Path
- Command-line Arguments
- Configured Triggers
- Execution Conditions

## 4. Hunting Commands

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

## 7. Conclusion

The scheduled task simulation demonstrated how Windows Task Scheduler can be used to execute applications automatically based on configured triggers. A safe Notepad application was used to understand and validate this persistence behavior.

# Persistence Detection and Analysis

## 1. Objective

Understand how different Windows persistence mechanisms can be investigated and correlated to identify potentially suspicious startup activity.

## 2. Investigation Areas

| Investigation | What We Examine | Potential Indicator |
|---|---|---|
| Registry Run Keys | Startup registry entries | Unknown executable or suspicious path |
| Scheduled Tasks | Task triggers and actions | Unexpected script or executable |

## 3. SOC Investigation Workflow

1. Identify an unfamiliar startup registry entry or scheduled task.
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

This lab provided hands-on experience investigating and simulating two Windows persistence mechanisms: Registry Run Keys and Scheduled Tasks.

Initially, existing Registry Run Key entries associated with OneDrive and Microsoft Edge were inspected. Subsequently, a safe Notepad application was configured through the Registry Run Key mechanism, successfully executed, and verified to open automatically after restarting the system.

The same controlled simulation was performed using Windows Task Scheduler to understand scheduled task-based persistence and automatic application execution.

These exercises helped demonstrate how legitimate Windows features can be abused to maintain persistence and how SOC analysts can investigate startup entries, scheduled tasks, executable paths, and related Windows event logs to identify potential persistence indicators.
