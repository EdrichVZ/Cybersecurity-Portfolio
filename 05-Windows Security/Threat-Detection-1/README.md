# Windows Threat Detection 1

## Overview

This repository documents my practical work from the **TryHackMe Windows Threat Detection 1** room.

The lab focused on detecting common **Initial Access** techniques against Windows systems. The investigations used Windows Security Event Logs and Sysmon telemetry to identify suspicious authentication, phishing, and removable-media activity.

The main objective was to understand how a Junior SOC Analyst could identify early signs of compromise and determine whether activity requires further investigation.

---

## Skills Practiced

* Windows Event Log analysis
* Sysmon event analysis
* RDP authentication investigation
* Failed and successful logon analysis
* Brute-force detection
* Phishing investigation
* Suspicious file detection
* Process creation analysis
* Removable-media investigation
* MITRE ATT&CK mapping
* Basic alert triage

---

## Windows Events Investigated

| Event     | Description      | Investigation Use                        |
| --------- | ---------------- | ---------------------------------------- |
| 4624      | Successful Logon | Identify successful authentication       |
| 4625      | Failed Logon     | Identify failed authentication attempts  |
| Sysmon 1  | Process Creation | Investigate suspicious process execution |
| Sysmon 11 | File Creation    | Identify newly created files             |

### RDP Investigation

A useful detection pattern is multiple failed authentication attempts followed by a successful login.

```text
4625
4625
4625
4625
4624
```

The analyst can investigate:

* Source IP address
* Target username
* Logon type
* Authentication time
* Number of failed attempts
* Whether a successful login followed the failures

**Logon Type 10** is particularly relevant when investigating Remote Desktop Protocol activity.

---

## Phishing Investigation

The lab also demonstrated how phishing can be used to establish Initial Access.

Suspicious indicators can include:

* Double file extensions
* Executable files disguised as documents
* LNK shortcut files
* Unexpected PowerShell execution
* Files launched from user download locations
* Unusual parent/child process relationships

A basic investigation could look like:

```text
User opens file
      ↓
Suspicious process starts
      ↓
PowerShell / command interpreter
      ↓
Additional file downloaded
      ↓
Potential malware execution
```

---

## Removable Media

The investigation also covered suspicious execution from removable media.

A SOC analyst should consider an executable launched from a removable drive suspicious when it is unexpected or inconsistent with normal user activity.

Useful information includes:

* Executed file
* File location
* User account
* Parent process
* Execution time
* Subsequent network activity

---

## Investigation Methodology

```text
Alert / Event
     ↓
Identify User & Source
     ↓
Review Event Details
     ↓
Check Related Events
     ↓
Build Timeline
     ↓
Identify Attack Technique
     ↓
Determine Severity
     ↓
Document / Escalate
```

---

## MITRE ATT&CK

Relevant techniques included:

* **T1133** — External Remote Services
* **T1190** — Exploit Public-Facing Application
* **T1566** — Phishing
* **T1566.001** — Spearphishing Attachment
* **T1091** — Replication Through Removable Media

---

## Key Takeaways

This lab helped develop basic skills in:

* Recognising suspicious authentication patterns
* Investigating Windows Event IDs
* Understanding RDP-related activity
* Identifying suspicious process execution
* Investigating potentially malicious files
* Using multiple events to build an attack timeline
* Mapping activity to MITRE ATT&CK

The main lesson was that **a single event often provides limited context**. Correlating multiple events can provide a much clearer picture of potential compromise.

---

## Lab

**Platform:** TryHackMe
**Room:** Windows Threat Detection 1

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.
