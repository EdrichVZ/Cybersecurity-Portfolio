# Windows Threat Detection 2

## Overview

This repository documents my practical work from the **TryHackMe Windows Threat Detection 2** room.

The lab focused on detecting attacker activity **after Initial Access**, with an emphasis on Windows Discovery, Collection, data staging, and the transfer of tools or information.

The goal was to understand how a SOC analyst can use Windows and Sysmon telemetry to identify what an attacker is doing after gaining access to a system.

---

## Skills Practiced

* Windows Event Log analysis
* Sysmon investigation
* Process tree analysis
* Command-line investigation
* Account discovery
* File and directory discovery
* Process discovery
* Security tool discovery
* Credential-related file discovery
* Clipboard activity investigation
* Data staging detection
* Network activity investigation
* Ingress Tool Transfer detection
* MITRE ATT&CK mapping

---

## Process Investigation

One of the most useful techniques when investigating Windows activity is examining the **process tree**.

For example:

```text
explorer.exe
     │
     └── powershell.exe
             │
             └── suspicious.exe
```

The process itself may not immediately appear malicious.

However, the relationship between:

* Parent process
* Child process
* Command line
* User
* Execution time

can provide important context.

---

## Discovery Activity

Attackers commonly perform reconnaissance after compromising a Windows host.

Examples include discovering:

* Users and accounts
* Running processes
* Files and directories
* Security software
* System information
* Network configuration

From a SOC perspective, unusual combinations of discovery commands can indicate that an attacker is actively exploring the compromised environment.

---

## File & Credential Discovery

Attackers may search the filesystem for information that can help them move further into an environment.

Examples include:

```text
Documents
Configuration files
SSH keys
Credential files
Browser-related data
Network information
```

The important detection question is not simply:

> "Was a file accessed?"

Instead:

> "Why was this user or process searching for this type of information?"

---

## Clipboard Activity

Clipboard contents can contain sensitive information such as:

* Passwords
* Tokens
* API keys
* Internal information
* Data copied from applications

Clipboard collection can therefore become relevant when investigating possible credential or information theft.

---

## Data Staging

Before data is exfiltrated, attackers may first collect and stage it locally.

A simplified sequence is:

```text
Find Data
   ↓
Collect Data
   ↓
Stage Data
   ↓
Compress / Prepare
   ↓
Transfer Data
```

This means suspicious archive creation or unusual file aggregation can be an important investigation lead.

---

## Ingress Tool Transfer

Attackers may download additional tools after gaining access to a system.

The analyst can investigate:

* Which process downloaded the file
* Where the file was saved
* Which account performed the action
* What command was executed
* Whether the downloaded file was subsequently executed

This can help establish the relationship between **tool transfer and subsequent attacker activity**.

---

## Investigation Methodology

```text
Suspicious Process
       ↓
Identify User
       ↓
Review Command Line
       ↓
Investigate Parent Process
       ↓
Identify Discovery / Collection Activity
       ↓
Check File & Network Activity
       ↓
Build Timeline
       ↓
Map to MITRE ATT&CK
       ↓
Document / Escalate
```

---

## MITRE ATT&CK

Relevant techniques included:

* **T1087** — Account Discovery
* **T1057** — Process Discovery
* **T1083** — File and Directory Discovery
* **T1115** — Clipboard Data
* **T1074** — Data Staged
* **T1105** — Ingress Tool Transfer
* **T1041** — Exfiltration Over C2 Channel

---

## Key Takeaways

This lab helped develop an understanding of how attackers behave **after gaining access to a Windows system**.

Important SOC concepts practiced included:

* Following process relationships
* Analysing command-line activity
* Identifying unusual discovery behaviour
* Investigating sensitive file searches
* Recognising data staging
* Investigating downloaded tools
* Connecting multiple events into an attack timeline

The main lesson was that **post-compromise activity often involves many seemingly normal Windows commands**. Context and correlation are therefore important when determining whether activity is malicious.

---

## Lab

**Platform:** TryHackMe
**Room:** Windows Threat Detection 2

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.
