# Introduction:

This repository documents the incident triage, log analysis, and investigation workflows completed as part of a TryHackMe SOC simulation lab. The project demonstrates real-world SOC analyst capabilities in evaluating security events, separating background operational noise from true intrusions, and formulating response plans under telemetry constraints.

## Company Information
All relevant Company information can be found at: [Company Information](Screenshots/Company-Information/Information.md)

## Objectives

* **SIEM Alert Processing:** Systematically investigate and process incoming alerts using the **TryDetectThis** monitoring application and the integrated **TryHackMe SIEM** environment.
* **Alert Classification:** Analyze process executions, network connections, and host artifacts to categorize each alert as a **True Positive (TP)** or **False Positive (FP)** across 17 total cases.
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
| **8815** | Possible Brute Force Attack on SSH | Low | Brute Force | No | [View Report](Case-Reports/8815-TP.md) |
| **8817** | Possible Multiple Unauthorized File Enumeration Attempts | Medium | Reconnaissance | No | [View Report](Case-Reports/8817-TP.md) |
| **8818** | Possible Brute Force Attack on SSH | Low | Brute Force | No | [View Report](Case-Reports/8818-TP.md) |
| **8821** | Possible Brute Force Attack on Web Application | Medium | Credential Access | No | [View Report](Case-Reports/8821-TP.md) |
| **8824** | Email Sent to External Recipient with Suspicious Top-Level Domain (TLD) | Low | Phishing | Yes | [View Report](Case-Reports/8824-TP.md) |
| **8825** | Possible Brute Force Attack on Web Application | Medium | Credential Access | Yes | [View Report](Case-Reports/8825-TP.md) |
| **8829** | Suspicious Parameter in Web Request | High | Post-Exploitation Activity | Yes | [View Report](Case-Reports/8829-TP.md) |
| **8830** | Suspicious Parameter in Web Request | High | Post-Exploitation Activity | Yes | [View Report](Case-Reports/8830-TP.md) |

---

**False Positives**

| Alert ID | Alert Name | Severity | Type | Escalation Needed? | Report Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **8814** | Email Sent to External Recipient with Suspicious Top-Level Domain (TLD) | Low | Phishing | No | [View Report](Case-Reports/8814-FP.md) |
| **8816** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8816-FP.md) |
| **8819** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8819-FP.md) |
| **8820** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8820-FP.md) |
| **8822** | Email Sent to External Recipient with Suspicious Top-Level Domain (TLD) | Low | Phishing | No | [View Report](Case-Reports/8822-FP.md)
| **8823** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8823-FP.md) |
| **8826** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8826-FP.md) |
| **8827** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8827-FP.md) |
| **8828** | Inbound Traffic from Suspicious IP Address | Low | Exfiltration | No | [View Report](Case-Reports/8828-FP.md) |


# Conclusion

| **Metric**               | **Details**       |
| :----------------------- | :---------------- |
| **Total Cases Analyzed** | 17 (8814 to 8830) |
| **False Positive Count** | 9 cases (53%)     |
| **True Positive Count**  | 8 cases (47%)     |

The most important lesson from this investigation was the value of **alert correlation and understanding the context behind multiple related alerts**. Several alerts that initially appeared to be separate security events were actually part of a larger attack sequence involving the same attacker IP address, `24.48.63.112`. Alert 8817 identified automated directory and file enumeration against `thetrydaily.thm`, followed by Alert 8821 showing a web application brute-force attempt against `/admin-login.php`. Alert 8825 then showed that the same attacker successfully authenticated to the administrative portal, providing evidence of **successful account compromise**.

The investigation then demonstrated how post-compromise activity can quickly escalate. Alert 8829 showed that the attacker used the compromised administrative access to upload `easy-simple-php-webshell.php` to the production web server. Alert 8830 subsequently showed the attacker using the webshell to execute an arbitrary command and modify the WordPress `footer.php` file. This progression provided clear evidence of **post-exploitation activity, unauthorized file modification, persistence, and potential remote code execution**. Correlating Alerts 8825, 8829, and 8830 was therefore important for understanding the full scope of the incident rather than treating each alert as an isolated event.

The investigation also demonstrated the importance of identifying **continued automated attack activity**. Alerts 8815 and 8818 showed repeated SSH brute-force attempts against the `admin` account from `182.132.25.71`. Although these attempts were unsuccessful, the repeated activity indicated an automated authentication attack. Because there was no evidence of a successful login or subsequent compromise in these alerts, they were classified as True Positives without requiring escalation.

Another important part of the investigation was recognizing and properly handling **False Positives**. Alerts 8816, 8819, 8820, 8823, 8826, 8827, and 8828 repeatedly flagged traffic from the internal workstation `10.20.2.16` as suspicious inbound traffic. Reviewing the source address and network context showed that this was legitimate RFC 1918 internal traffic rather than malicious external traffic. Similarly, Alerts 8814 and 8822 involved legitimate outbound legal communications to an external `.tech` domain. These cases demonstrated why a SOC analyst should review the **source, destination, IP address type, user, protocol, application, and business context** before escalating an alert.

The investigation also highlighted the importance of **escalation based on the impact and stage of an attack**. Earlier reconnaissance and unsuccessful brute-force attempts could be contained through firewall or WAF controls and monitoring. However, Alert 8825 required escalation because authentication to the administrative portal was successful, while Alerts 8829 and 8830 required immediate escalation because the attacker progressed to **webshell deployment and arbitrary command execution** on the production server.

Overall, this investigation provided practical experience with:

* **Alert triage and classification**
* **True Positive vs False Positive analysis**
* **Alert correlation**
* **Web application reconnaissance**
* **Directory and file enumeration**
* **SSH brute-force detection**
* **Web application brute-force detection**
* **Successful authentication and account compromise identification**
* **Webshell detection**
* **Post-exploitation activity**
* **Arbitrary command execution**
* **Unauthorized file modification**
* **Persistence indicators**
* **Potential credential and database information exposure**
* **Internal vs external IP address analysis**
* **False positive identification and detection tuning**

