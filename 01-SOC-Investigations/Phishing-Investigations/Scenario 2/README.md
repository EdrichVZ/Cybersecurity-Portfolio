# Introduction:

This repository documents the incident triage, log analysis, and investigation workflows completed as part of the **TryHackMe: Phishing Unfolding** SOC simulation lab. The project demonstrates real-world SOC analyst capabilities in evaluating security events, separating background operational noise from true intrusions, and formulating response plans under telemetry constraints.

## Objectives

* **SIEM Alert Processing:** Systematically investigate and process incoming alerts using the **TryDetectThis** monitoring application and the integrated **TryHackMe SIEM** environment.
* **Alert Classification:** Analyze process executions, network connections, and host artifacts to categorize each alert as a **True Positive (TP)** or **False Positive (FP)** across 40 total cases.
* **Threat Escalation:** Identify active intrusion vectors, reconstruct attack lifecycles, and escalate critical security incidents requiring immediate containment.
* **Standardized Documentation:** Produce detailed, structured incident case reports covering executive summaries, affected entities, threat indicators, triage analysis, and actionable remediation steps.

## Tools & Environment

| Tool / System | Application in Investigation |
| :--- | :--- |
| **TryHackMe SIEM** | Simulated SOC SIEM environment providing central log management, event correlation, and alert monitoring. |
| **TryDetectThis** | Primary SOC alert queue and incident management application used to receive, triage, and process telemetry alerts. |
| **Splunk Enterprise** | SIEM platform used to query Sysmon logs, correlate process creation events, trace DNS queries, and extract IOCs under limited log retention constraints. |

**True Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1005** | Suspicious Attachment found in email | Low | Phishing | Yes | [View Report](Case-Reports/ALT-1005-TP.md) |
| **1020** | Powershell Script in Downloads Folder | Low | Execution | Yes | [View Report](Case-Reports/ALT-1020-TP.md) |
| **1022** | Network drive mapped to a local drive | Medium | Execution | Yes | [View Report](Case-Reports/ALT-1022-TP.md) |
| **1023** | Suspicious Parent Child Relationship | Low | Process | Yes | [View Report](Case-Reports/ALT-1023-TP.md) |
| **1024** | Network drive disconnected from a local drive | Medium | Execution | Yes | [View Report](Case-Reports/ALT-1024-TP.md) |
| **1025** | Suspicious Parent Child Relationshipt | High | Process | Yes | [View Report](Case-Reports/ALT-1025-TP.md) |
| **1026** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1026-TP.md) |
| **1027** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1027-TP.md) |
| **1028** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1028-TP.md) |
| **1029** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1029-TP.md) |
| **1030** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1030-TP.md) |
| **1031** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1031-TP.md) |
| **1032** |Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1032-TP.md) |
| **1033** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1033-TP.md) |
| **1034** | Suspicious Parent Child Relationship | High | Process | Yes | [View Report](Case-Reports/ALT-1034-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1000** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1000-FP.md) |
| **1001** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1001-FP.md) |
| **1002** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1002-FP.md) |
| **1003** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1003-FP.md) |
| **1004** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1004-FP.md) |
| **1006** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1006-FP.md) |
| **1007** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1007-FP.md) |
| **1008** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1008-FP.md) |
| **1009** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1009-FP.md) |
| **1010** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1010-FP.md) |
| **1011** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1011-FP.md) |
| **1012** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1012-FP.md) |
| **1013** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1013-FP.md) |
| **1014** | Suspicious email from external domain.e | Low | Phishing | No | [View Report](Case-Reports/ALT-1014-FP.md) |
| **1015** | Suspicious Parent Child Relationship | Low | Process| No | [View Report](Case-Reports/ALT-1015-FP.md) |
| **1016** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1016-FP.md) |
| **1017** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1017-FP.md) |
| **1018** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1018-FP.md) |
| **1019** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1019-FP.md) |
| **1021** | Suspicious Parent Child Relationship | Low | Process | No | [View Report](Case-Reports/ALT-1021-FP.md) |
| **1035** | Suspicious email from external domain. | Low | Phishing | No | [View Report](Case-Reports/ALT-1035-FP.md) |

# Conclusion:

| Metric | Details |
| :--- | :--- |
| **Total Cases Analyzed** | 35 (1000 to 1035) |
| **False Positive Count** | 20 cases (57%) |
| **True Positive Count** | 15 cases (43%) |

---

User account michael.ascot on host win-3450 was compromised via a probable phishing email, leading to an automated execution sequence that mapped restricted network shares, staged sensitive files in C:\Users\michael.ascot\downloads\exfiltration\, and covertly exfiltrated encoded payload chunks using nslookup.exe via DNS tunneling to haz4rdw4re.io. To contain this incident and prevent further impact, host win-3450 must remain isolated from the network for full re-imaging, all active sessions for user michael.ascot must be revoked alongside a forced domain password reset, *.haz4rdw4re.io and related subdomains must be blocked at the DNS and firewall level, and internal DNS resolver logs should be queried immediately to determine the full volume and timeline of exfiltrated data.

## Attack Lifecycle: Host win-3450 Compromise Chain

10 True Positive cases reconstruct an automated, end-to-end kill chain targeting corporate financial assets:

1. **Initial Access & Execution:** A phishing payload initiated execution inside `C:\Users\michael.ascot\downloads\`, spawning persistent PowerShell process `PID: 3728`.
2. **Discovery & Share Mapping (ALT-27):** Executed `net.exe` to map restricted share `\\FILESRV-01\SSF-FinancialRecords` to local drive `Z:`. *(MITRE ATT&CK T1135, T1021.002)*
3. **Data Collection & Staging (ALT-28):** Executed `Robocopy.exe /E` to copy target contents from `Z:\` into local staging folder `C:\Users\michael.ascot\downloads\exfiltration\`. *(MITRE ATT&CK T1039, T1074)*
4. **Defense Evasion (ALT-29):** Executed `net.exe use Z: /delete` to unmap the network drive and clear local evidence. *(MITRE ATT&CK T1070)*
5. **Covert Exfiltration via DNS Tunneling (ALT-31 – ALT-40):** Automated process spawned `nslookup.exe` binary calls carrying base64-encoded file chunks via UDP Port 53 queries targeting `*.h4z4rdw4re.io` and `*.haz4rdw4re.io`. *(MITRE ATT&CK T1071.004, T1048.003, T1132.001)*

---

## Remediation Plan

> * **Host Isolation:** Maintain strict network isolation on host `win-3450` until full forensic re-imaging is completed.
> * **Credential Revocation:** Revoke all active user sessions and force an immediate domain password reset for account `michael.ascot`.
> * **Infrastructure Blocking:** Enforce global DNS sinkholing and enterprise firewall blocks for `*.haz4rdw4re.io` and `*.h4z4rdw4re.io`.
> * **Forensic Audit:** Query internal DNS resolver logs immediately to reconstruct the complete exfiltration timeline and total data volume stolen.
