Windows Threat Detection 2
Overview

This repository documents my practical work from the Windows Threat Detection 2 room.

The lab focused on identifying attacker activity after initial access, using Windows Event Logs and Sysmon to investigate Discovery, Collection, and data transfer activity.

Skills Practiced
Windows Event Log analysis
Sysmon Event ID 1 analysis
Process tree investigation
Windows Discovery techniques
Suspicious command-line analysis
Sensitive file discovery
Data collection and staging
Clipboard activity detection
Network/exfiltration investigation
Ingress Tool Transfer detection
Key Events
Event ID	Description	SOC Use
1	Sysmon Process Creation	Investigate commands and process relationships
22	Sysmon DNS Query	Identify suspicious domains and network activity
4688	Process Creation	Investigate process execution
Investigation Approach

The basic investigation process was:

Suspicious Process
       ↓
Identify Parent Process
       ↓
Review Command Line
       ↓
Identify Attacker Activity
       ↓
Check Files / Network Activity
       ↓
Map to MITRE ATT&CK
       ↓
Document & Escalate
Activity Investigated

The lab provided practical exposure to detecting:

System and user discovery
Security tool discovery
Sensitive file searches
Credential and SSH key collection
Clipboard collection
Data staging
Data exfiltration
Downloading tools using legitimate Windows utilities
MITRE ATT&CK

Relevant techniques included:

T1087 — Account Discovery
T1057 — Process Discovery
T1083 — File and Directory Discovery
T1555.003 — Credentials from Web Browsers
T1114 — Email Collection
T1115 — Clipboard Data
T1074 — Data Staged
T1041 — Exfiltration Over C2 Channel
T1105 — Ingress Tool Transfer
Key Takeaway

This lab helped develop the fundamentals of investigating post-compromise Windows activity.

The main focus was learning how a SOC analyst can use process creation, command-line information, file activity, and DNS/network telemetry to reconstruct what an attacker was doing on a compromised host.

Lab

Room: Windows Threat Detection 2

This repository contains my own learning notes and investigation methodology rather than a reproduction of the room's answers.
