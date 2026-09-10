# Windows Threat Detection 1

## Overview

This repository documents my practical work from the **Windows Threat Detection 1** room.

The lab focused on identifying common Windows **Initial Access** techniques and using Windows security telemetry to support basic SOC investigations.

## Skills Practiced

* Windows Event Log analysis
* Sysmon event analysis
* RDP login investigation
* Failed vs successful authentication analysis
* Phishing detection
* Process and file activity investigation
* Removable media detection
* Basic MITRE ATT&CK mapping

## Key Windows Events

| Event ID  | Description      | SOC Use                               |
| --------- | ---------------- | ------------------------------------- |
| 4624      | Successful Logon | Identify successful authentication    |
| 4625      | Failed Logon     | Identify brute-force/password attacks |
| Sysmon 1  | Process Creation | Investigate suspicious processes      |
| Sysmon 11 | File Creation    | Identify suspicious files             |

## Investigation Approach

The basic investigation process used was:

```text
Alert / Event
     ↓
Identify User & Source
     ↓
Examine Event Details
     ↓
Check Related Activity
     ↓
Identify Attack Technique
     ↓
Determine Severity / Escalate
```

## MITRE ATT&CK

Relevant techniques included:

* **T1133** — External Remote Services
* **T1566** — Phishing
* **T1091** — Replication Through Removable Media
* **T1059.001** — PowerShell

## Key Takeaway

This lab helped develop the fundamentals of investigating suspicious Windows activity by combining **authentication events, process activity, and file activity** to identify potential Initial Access techniques.

## Lab

**Room:** Windows Threat Detection 1

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.

