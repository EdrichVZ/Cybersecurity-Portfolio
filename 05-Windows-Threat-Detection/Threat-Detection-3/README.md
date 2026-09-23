# Windows Threat Detection 3

## Overview

This repository documents my practical work from the **TryHackMe Windows Threat Detection 3** room.

The lab focused on detecting **Command and Control (C2), Persistence, and Impact-related activity** on Windows systems.

The objective was to understand how attackers can maintain access to a compromised system and how a SOC analyst can identify suspicious persistence mechanisms through Windows telemetry.

---

## Skills Practiced

* Windows Event Log analysis
* Sysmon analysis
* Process tree investigation
* C2 investigation
* Suspicious network activity
* Account creation detection
* Privilege-related activity
* Windows service investigation
* Scheduled task investigation
* Registry Run Key investigation
* Startup folder investigation
* Persistence detection
* MITRE ATT&CK mapping

---

## Command & Control

Command and Control allows an attacker to communicate with a compromised system.

A basic C2 investigation can examine:

* Destination IP address
* Domain name
* DNS queries
* Process responsible for the connection
* Connection timing
* Repeated connections
* Unusual outbound traffic

A suspicious process making repeated outbound connections can become significantly more interesting when combined with other indicators of compromise.

---

## Persistence

Persistence allows an attacker to maintain access even after a system restart or user logoff.

The lab covered several Windows persistence mechanisms.

### Windows Services

Attackers can create or modify services to execute malicious programs.

A SOC analyst should investigate:

* Newly created services
* Unusual service names
* Unexpected executable paths
* Services created shortly before suspicious activity

---

### Scheduled Tasks

Scheduled Tasks can be abused to execute programs automatically.

Useful investigation points include:

* Task name
* Creation time
* User account
* Program executed
* Trigger configuration
* Parent process

---

### Registry Run Keys

Programs configured through Registry Run Keys can execute automatically when a user logs in.

Suspicious entries should be investigated for:

* Unknown executable paths
* Recently created entries
* Unusual filenames
* Programs running from temporary or user-writable locations

---

### Startup Folder

The Windows Startup folder can also be used to execute programs when a user logs in.

Unexpected executables or scripts in startup locations should therefore be investigated.

---

## Account Activity

Attackers may create additional accounts to maintain access.

Important questions include:

```text
Who created the account?
When was it created?
Was it added to a privileged group?
Was the account used afterwards?
```

An unexpected account creation followed by privileged activity can be a strong indicator of compromise.

---

## Investigation Methodology

```text
Suspicious Activity
       ↓
Identify User / Process
       ↓
Review Event Details
       ↓
Investigate Persistence
       ↓
Check Network Activity
       ↓
Build Attack Timeline
       ↓
Map to MITRE ATT&CK
       ↓
Assess & Escalate
```

---

## MITRE ATT&CK

Relevant techniques included:

* **T1071** — Application Layer Protocol
* **T1136** — Create Account
* **T1053** — Scheduled Task/Job
* **T1543** — Create or Modify System Process
* **T1547.001** — Registry Run Keys / Startup Folder

---

## Key Takeaways

This lab helped develop practical awareness of:

* How attackers establish persistence
* How suspicious services and scheduled tasks can be identified
* How new accounts can indicate malicious activity
* How Registry Run Keys can be abused
* How process and network activity can help identify C2
* How multiple Windows events can be correlated during an investigation

The main lesson was that **persistence mechanisms can look like legitimate Windows administration activity**. Analysts therefore need to consider the process, user, timing, location, and surrounding activity when deciding whether something is suspicious.

---

## Lab

**Platform:** TryHackMe
**Room:** Windows Threat Detection 3

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.
