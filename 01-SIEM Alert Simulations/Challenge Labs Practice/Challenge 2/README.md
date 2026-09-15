# Introduction:

This repository documents the incident triage, log analysis, and investigation workflows completed as part of a TryHackMe SOC simulation lab. The project demonstrates real-world SOC analyst capabilities in evaluating security events, separating background operational noise from true intrusions, and formulating response plans under telemetry constraints.

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
| **3256** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/102-TP.md) |
| **3257** | Successful Brute Force | High | Brute Force | Yes | [View Report](Case-Reports/103-TP.md) |
| **3258** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | Yes | [View Report](Case-Reports/104-TP.md) |
| **3259** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/107-TP.md) |
| **3260** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/107-TP.md) |
| **3263** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/107-TP.md) |
| **3266** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/107-TP.md) |
| **3267** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/107-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **3255** | Unusual Port In Outbound Connection | High | Post-Compromise Activity | No | [View Report](Case-Reports/101-FP.md) |
| **3261** | Unusual Port In Outbound Connection | High | Post-Compromise Activity | No | [View Report](Case-Reports/105-FP.md) |
| **3262** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/106-FP.md) |
| **3264** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/106-FP.md) |
| **3265** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/106-FP.md) |

# Conclusion:

| Metric | Details |
| :--- | :--- |
| **Total Cases Analyzed** |  ( 13 (3255 to 3267 ) |
| **False Positive Count** |  5 cases (38%) |
| **True Positive Count** |  8 cases (62%) |

The most significant finding was the relationship between Alerts **102–104, 107, 110, and 114**. These alerts formed a clear multi-stage attack chain involving:

* Obfuscated PowerShell execution.
* Outbound C2 communication to `85.203.21.23`.
* Creation of the rogue account `Michael.Myres`.
* External RDP access to `IT-TRYHATME`.
* Interactive command execution using the compromised account.
* Deletion of Volume Shadow Copies using `vssadmin.exe`.

Rather than investigating each alert in isolation, correlating the events by **user, host, process ID, IP address, and timeline** provided stronger evidence of an active compromise.

The false positives demonstrated the importance of understanding normal business and administrative activity. Legitimate backup operations, internal RDP administration, browser traffic, and policy violations involving stored credentials or macro-enabled documents were identified through contextual analysis rather than being automatically treated as malicious.

---

# Conclusion

The most important lesson was the value of **alert correlation**. Alert 102 initially identified suspicious PowerShell activity, but subsequent alerts provided additional evidence that the workstation had been compromised. The same PowerShell process was linked to external C2 communication in Alert 103, which was followed by the creation of the `Michael.Myres` persistence account in Alert 104. Alert 107 then showed external RDP access from the same C2 IP, while Alert 110 demonstrated interactive command execution using the newly created account. Finally, Alert 114 showed destructive activity through the deletion of Volume Shadow Copies.

This progression demonstrates how individual alerts can provide only part of the picture. Correlating multiple events allowed the activity to be understood as a **multi-stage attack rather than a collection of unrelated alerts**.

The exercise also reinforced the importance of avoiding unnecessary escalation. Several alerts initially appeared suspicious but were determined to be legitimate administrative activity or policy compliance issues after reviewing the associated user, host, process, and business context.

Overall, this investigation provided practical experience with:

* **Alert triage and classification**
* **True Positive vs False Positive analysis**
* **Alert correlation**
* **Process and parent/child process analysis**
* **PowerShell investigation**
* **Network and C2 identification**
* **Persistence detection**
* **RDP investigation**
* **Command execution analysis**
* **Defense evasion detection**
* **Escalation and remediation recommendations**

