# Introduction:

This repository documents the incident triage, log analysis, and investigation workflows completed as part of a TryHackMe SOC simulation lab. The project demonstrates real-world SOC analyst capabilities in evaluating security events, separating background operational noise from true intrusions, and formulating response plans under telemetry constraints.

## Company Information
All relevant Company information can be found at: [Company Information](Screenshots/Company-Information/Information.md)

## Objectives

* **SIEM Alert Processing:** Systematically investigate and process incoming alerts using the **TryDetectThis** monitoring application and the integrated **TryHackMe SIEM** environment.
* **Alert Classification:** Analyze process executions, network connections, and host artifacts to categorize each alert as a **True Positive (TP)** or **False Positive (FP)** across 20 total cases.
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
| **1004** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1004-TP.md) |
| **1006** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1006-TP.md) |
| **1007** | Download File To Potentially Suspicious Directory Via Wget | Critical | Execution | Yes | [View Report](Case-Reports/1007-TP.md) |
| **1008** | Chmod Suspicious Directory | High | Execution | Yes | [View Report](Case-Reports/1008-TP.md) |
| **1009** | Chmod Suspicious Directory | High | Execution | Yes | [View Report](Case-Reports/1009-TP.md) |
| **1010** | Suspicious DNS Query | High | DNS | Yes | [View Report](Case-Reports/1010-TP.md) |
| **1012** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1012-TP.md) |
| **1013** | Suspicious DNS Query | High | DNS | Yes | [View Report](Case-Reports/1013-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1000** | Download File To Potentially Suspicious Directory Via Wget | Critical | Execution | No | [View Report](Case-Reports/1000-FP.md) |
| **1001** | Chmod Suspicious Directory | High | Execution | No | [View Report](Case-Reports/1001-FP.md) |
| **1002** | Hacking Website Blocked | Low | Malware | No | [View Report](Case-Reports/1002-FP.md) |
| **1003** | Process Discovery | High | Execution | No | [View Report](Case-Reports/1003-FP.md) |
| **1005** | Potential Suspicious Change To Sensitive/Critical Files | High | Execution | No | [View Report](Case-Reports/1005-FP.md) |
| **1011** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1011-FP.md) |
| **1014** | Hacking Website Blocked | Low | Malware | No | [View Report](Case-Reports/1014-FP.md) |
| **1015** | Hacking Website Blocked | Low | Malware | No | [View Report](Case-Reports/1015-FP.md) |
| **1016** | Shell Invocation via Apt | High | Execution | No | [View Report](Case-Reports/1016-FP.md) |
| **1017** | Shell Invocation via Apt | High | Execution | No | [View Report](Case-Reports/1017-FP.md) |
| **1018** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1018-FP.md) |
| **1019** | Phishing Website Blocked | Low | Phishing | No | [View Report](Case-Reports/1019-FP.md) |

# Conclusion

| **Metric**               | **Details**       |
| :----------------------- | :---------------- |
| **Total Cases Analyzed** | 20 (1000 to 1019) |
| **False Positive Count** | 12 cases (60%)    |
| **True Positive Count**  | 8 cases (40%)     |

The most important lesson from this investigation was the value of **alert correlation and understanding the context surrounding Linux command execution, network activity, and security controls**. Several alerts appeared suspicious when viewed individually, but correlating the user, host, command line, destination, and surrounding events made it possible to distinguish legitimate administrative activity from genuine malicious behavior.

One of the clearest attack sequences occurred on `Lin-001` involving the user `michael.ascot`. Alert 1007 showed `wget` being used to download `installer.sh` from the external IP `159.65.241.15` into `/tmp`. Alert 1008 then showed `chmod +x installer.sh`, making the downloaded script executable. Alert 1009 subsequently showed `chmod +x /home/michael.ascot/.local/update` being executed as `root`. When correlated, these events demonstrated a progression from **payload download to execution preparation and privileged modification**, making the surrounding context significantly more suspicious than any single command viewed in isolation.

The investigation also highlighted the importance of recognizing potential **Command-and-Control (C2) activity through DNS**. Alerts 1010 and 1013 detected suspicious DNS queries from the internal host `192.168.0.10` to domains associated with known C2-style detection patterns. Alert 1010 recorded a query for `a1b2c3.tyhatme.xyz`, while Alert 1013 later recorded another suspicious query for `wer484.tyhatme.xyz`. Repeated suspicious DNS activity from the same internal source demonstrated why analysts should correlate DNS events over time rather than treating each query independently. Repeated or regularly timed DNS queries can indicate **beaconing or communication with attacker-controlled infrastructure**.

Another important lesson was distinguishing suspicious-looking Linux commands from **legitimate system administration**. Alerts 1016 and 1017 triggered on the use of `apt-get`, a utility that can potentially be abused for shell invocation or command execution. However, the actual commands were `apt-get update` followed by `apt-get upgrade`. This sequence is consistent with normal Linux package maintenance. Correlating the two alerts prevented legitimate administrative activity from being incorrectly escalated simply because the detection rule identified a potentially abusable utility.

Several alerts also demonstrated the importance of validating the **purpose and reputation of destinations before classifying web activity as malicious**. Security tools generated alerts for access to websites such as `abuseipdb.com`, `shodan.io`, and `exploit-db.com`. Although these websites are associated with cybersecurity, threat intelligence, vulnerability research, and offensive-security information, accessing them does not automatically indicate malicious activity. For a SOC analyst or security administrator, these resources may be completely legitimate. This reinforced the importance of considering **business context and user activity** rather than relying only on the category assigned by a firewall rule.

The phishing alerts provided another example of why the **effectiveness of preventative controls** must be considered during triage. Alerts involving suspicious domains such as `slak.com` and `zo0m.us` resembled legitimate services and could represent typosquatting or phishing attempts. However, the firewall action was `blocked`, meaning access to these destinations was prevented. With no evidence of successful access, credential submission, malware execution, or subsequent compromise, these events did not require escalation. These alerts demonstrated how analysts should distinguish between an **attempted security event and a successful compromise**.

The investigation also reinforced that **alert severity alone should not determine escalation**. Some High-severity alerts were ultimately associated with legitimate activity, while other events became more significant only after they were correlated with preceding or subsequent activity. Reviewing the command line, process, parent process, user account, privilege level, source and destination addresses, firewall action, and related alerts provided a much stronger basis for determining the actual risk.

Overall, this investigation provided practical experience with:

* **Alert triage and classification**
* **True Positive vs False Positive analysis**
* **Alert correlation**
* **Linux process and command-line analysis**
* **Sysmon for Linux event analysis**
* **`wget` download activity**
* **`chmod` permission modification**
* **Suspicious directory and file activity**
* **Root and privileged command execution**
* **DNS query analysis**
* **Potential C2 beaconing detection**
* **Firewall log analysis**
* **Phishing and typosquatted domain identification**
* **Security-tool website validation**
* **Prevented vs successful attack analysis**
* **Linux package-management activity**
* **Legitimate administrative activity identification**
* **IOC identification**
* **Escalation decision-making**
* **False positive identification**
* **Detection-rule tuning considerations**

Overall, the exercise demonstrated that effective SOC analysis is not simply about identifying suspicious alerts. The key skill is determining **what happened before and after an alert, whether the activity was successful, whether it fits legitimate user behavior, and whether multiple events form part of a larger attack chain**. Correlating events across endpoint, DNS, and firewall telemetry provided the context needed to make more accurate classification and escalation decisions.
