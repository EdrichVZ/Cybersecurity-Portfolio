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
| **102** | Suspicious Encoded Service Command Executed | Critical | Execution | Yes | [View Report](Case-Reports/102-TP.md) |
| **103** | Suspicious Network Connection Established | High | Network | Yes | [View Report](Case-Reports/103-TP.md) |
| **104** | New Admin Account Creation | High | Persistance | Yes | [View Report](Case-Reports/104-TP.md) |
| **107** | Suspicious RDP Connectionp | High | Network | Yes | [View Report](Case-Reports/107-TP.md) |
| **110** | Attacker Shell Execution | High | Network | Yes | [View Report](Case-Reports/110-TP.md) |
| **114** | System Shadow Copy Compromise | Critical | Execution | Yes | [View Report](Case-Reports/114-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **101** | Suspicious Internal Connection to Backup Server | Low | Network | No | [View Report](Case-Reports/101-FP.md) |
| **105** | Cleartext Credentials File Access | Medium | Execution | No | [View Report](Case-Reports/105-FP.md) |
| **106** | Suspicious Macro-Enabled Document Execution | Medium | Execution | No | [View Report](Case-Reports/106-FP.md) |
| **108** | Outbound HTTPS Connection | Low | Execution | No | [View Report](Case-Reports/108-FP.md) |
| **109** | Suspicious Macro-Enabled Document Execution | Medium | Execution | No | [View Report](Case-Reports/109-FP.md) |
| **111** | Suspicious Internal Connection to Backup Server | Low | Network | No | [View Report](Case-Reports/111-FP.md) |
| **112** | Internal RDP Connection | Low | Execution | No | [View Report](Case-Reports/112-FP.md) |
| **113** | Outbound HTTP Connection | Low | Execution | No | [View Report](Case-Reports/113-FP.md) |

# Conclusion:

| Metric | Details |
| :--- | :--- |
| **Total Cases Analyzed** |  ( 14 (101 to 114 ) |
| **False Positive Count** |  6 cases (43%) |
| **True Positive Count** |  8 cases (57%) |

---



---

## Remediation Plan



