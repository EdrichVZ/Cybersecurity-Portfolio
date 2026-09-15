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
| **3256** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3256-TP.md) |
| **3257** | Successful Brute Force | High | Brute Force | Yes | [View Report](Case-Reports/3257-TP.md) |
| **3258** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | Yes | [View Report](Case-Reports/3258-TP.md) |
| **3259** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3259-TP.md) |
| **3260** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3260-TP.md) |
| **3263** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3263-TP.md) |
| **3266** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3266-TP.md) |
| **3267** | Possible Brute Force Attempt | Medium | Brute Force | No | [View Report](Case-Reports/3267-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **3255** | Unusual Port In Outbound Connection | High | Post-Compromise Activity | No | [View Report](Case-Reports/3255-FP.md) |
| **3261** | Unusual Port In Outbound Connection | High | Post-Compromise Activity | No | [View Report](Case-Reports/3261-FP.md) |
| **3262** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/3262-FP.md) |
| **3264** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/3264-FP.md) |
| **3265** | Excessive Firewall Denies From Internal Source | High | Lateral Movement | No | [View Report](Case-Reports/3265-FP.md) |

# Conclusion:

| Metric | Details |
| :--- | :--- |
| **Total Cases Analyzed** |  ( 13 (3255 to 3267 ) |
| **False Positive Count** |  5 cases (38%) |
| **True Positive Count** |  8 cases (62%) |

# Conclusion

The most important lesson from this investigation was the value of **alert correlation and understanding the context behind an alert**. Alert 3256 initially identified repeated failed VPN authentication attempts against `j.mitchell` from the external IP `128.199.215.40`. Alert 3257 then showed that the same brute-force activity resulted in a successful VPN authentication, indicating that the account had been compromised. Shortly afterwards, Alert 3258 showed activity from the assigned VPN IP `10.30.3.16` attempting to communicate with internal network resources, providing evidence of **post-compromise network reconnaissance and possible lateral movement**.

The investigation also demonstrated how multiple alerts can be related even when they occur at different times or involve different accounts. Alerts 3259 and 3260 showed continued SSH brute-force activity against `jumphost_01` from the same external source IP, `180.101.88.223`, although both attempts were unsuccessful. Alerts 3266 and 3267 similarly showed continued VPN brute-force activity from `190.104.25.221` against different accounts. Correlating these events helped identify the activity as **automated authentication attacks rather than isolated login failures**.

The exercise also reinforced the importance of avoiding unnecessary escalation. Several alerts initially appeared suspicious but were determined to be legitimate activity after reviewing the surrounding context. Alerts 3255 and 3261 involved authorized TryHackMe VPN testing by `j.carter`, while Alerts 3262, 3264, and 3265 involved legitimate multicast and Windows network discovery traffic that was being blocked by firewall rules. These cases demonstrated why a SOC analyst should investigate the **user, source, destination, protocol, and business context** before classifying an alert as malicious.

Overall, this investigation provided practical experience with:

* **Alert triage and classification**
* **True Positive vs False Positive analysis**
* **Alert correlation**
* **Brute-force attack detection**
* **VPN authentication investigation**
* **Successful account compromise identification**
* **Network reconnaissance detection**
* **Lateral movement indicators**
* **SSH brute-force investigation**
* **Firewall and network traffic analysis**
* **Legitimate multicast traffic identification**
* **False positive identification and tuning**
* **Escalation and remediation recommendations**
