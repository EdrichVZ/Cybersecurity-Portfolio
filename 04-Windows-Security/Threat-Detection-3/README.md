# Windows Threat Detection 3

## Overview

This repository documents my practical work from the **Windows Threat Detection 3** room.

The lab focused on detecting **Command and Control (C2)** activity and identifying different methods attackers can use to maintain persistence on compromised Windows systems.

## Skills Practiced

* Windows Event Log analysis
* Sysmon analysis
* C2 investigation
* Process tree analysis
* Backdoor account detection
* Privilege escalation detection
* Windows service persistence
* Scheduled task persistence
* Registry Run Key persistence
* Startup folder persistence
* Basic MITRE ATT&CK mapping

## Key Detection Areas

| Activity           | SOC Detection Focus                     |
| ------------------ | --------------------------------------- |
| C2 communication   | Suspicious domains and network activity |
| Backdoor accounts  | New user creation and group membership  |
| Malicious services | Unexpected service creation             |
| Scheduled tasks    | Suspicious task creation and execution  |
| Run Keys           | Registry-based persistence              |
| Startup items      | Suspicious programs executed at startup |

## Investigation Approach

The basic investigation process was:

```text
Suspicious Activity
       ↓
Identify Process / User
       ↓
Review Event Details
       ↓
Check Parent / Child Processes
       ↓
Look for Persistence
       ↓
Identify C2 Activity
       ↓
Map to MITRE ATT&CK
       ↓
Document & Escalate
```

## MITRE ATT&CK

Relevant techniques included:

* **T1071** — Application Layer Protocol
* **T1136** — Create Account
* **T1543** — Create or Modify System Process
* **T1053** — Scheduled Task/Job
* **T1547.001** — Registry Run Keys / Startup Folder

## Key Takeaway

This lab helped develop the fundamentals of detecting **post-compromise persistence and Command and Control activity** on Windows systems.

The main focus was learning how a SOC analyst can use **Windows Event Logs, Sysmon, process relationships, account activity, and persistence mechanisms** to identify suspicious behaviour and support incident escalation.

## Lab

**Room:** Windows Threat Detection 3

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.

