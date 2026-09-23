# SOC Practical Exercises

This section contains scenario-based cybersecurity exercises focused on the **analytical and decision-making skills expected from a Junior SOC Analyst**.

The exercises cover incident response, vulnerability prioritization, MITRE ATT&CK analysis, IOC identification, phishing analysis, authentication and authorization failures, and security-control evaluation.

The goal is to practice **interpreting evidence, prioritizing risk, identifying attacker behavior, and deciding on appropriate response actions**.

---

## Exercises

| Exercise | Focus | Link |
| :--- | :--- | :--- |
| **Exercise 1 — Brute Force, Persistence & Incident Response** | Authentication attacks, privilege abuse, persistence, containment, and eradication. | [View Exercise](Exercises/Exercise-1.md) |
| **Exercise 2 — Vulnerability Prioritization** | CVSS, CISA KEV, exploitability, environmental context, and remediation priority. | [View Exercise](Exercises/Exercise-2.md) |
| **Exercise 3 — MITRE ATT&CK Sequence Analysis** | Attack-chain reconstruction, tactics, lateral movement, C2, and containment decisions. | [View Exercise](Exercises/Exercise-3.md) |
| **Exercise 4 — SOC Alert & IOC Analysis** | Host and network IOCs, PowerShell activity, C2 beaconing, and incident response. | [View Exercise](Exercises/Exercise-4.md) |
| **Exercise 5 — Phishing & Security Controls** | Email spoofing, SPF/DKIM/DMARC, IAM failures, PAM, MFA, and remediation. | [View Exercise](Exercises/Exercise-5.md) |

---

## Exercise Highlights

### Exercise 1 — Brute Force, Persistence & Incident Response

Analysis of a simulated compromise involving:

- Repeated failed authentication attempts
- Successful account access
- Privilege abuse
- Creation of a suspicious service-style account
- Scheduled-task persistence
- Containment and eradication decisions

---

### Exercise 2 — Vulnerability Prioritization

Risk-based vulnerability analysis considering:

- CVSS severity
- CISA Known Exploited Vulnerabilities (KEV)
- Public exploit availability
- Asset exposure
- Network placement
- Business impact
- Environmental context

The exercise demonstrates why vulnerability priority should not be based on CVSS score alone.

---

### Exercise 3 — MITRE ATT&CK Sequence Analysis

Attack activity was mapped across multiple stages including:

- Initial Access
- Execution
- Persistence
- Credential Access
- Command and Control
- Discovery
- Lateral Movement

The exercise focused on reconstructing attacker activity chronologically and identifying an appropriate containment point.

---

### Exercise 4 — SOC Alert & IOC Analysis

Analysis of suspicious host and network activity including:

- Abnormal authentication
- Outlook spawning PowerShell
- Encoded command execution
- Registry persistence
- C2 beaconing
- Data staging
- Host and network IOC classification
- Incident containment

---

### Exercise 5 — Phishing & Security Controls

Phishing and identity-security analysis covering:

- Email spoofing
- SPF, DKIM, and DMARC failures
- Social engineering
- Typosquatted domains
- Host and network IOCs
- Authentication failures
- Authorization failures
- Privileged Access Management
- MFA
- Containment and eradication

---

## Reference Notes

The repository also contains supporting cybersecurity reference notes covering topics such as:

- CVSS
- Indicators of Compromise
- Identity and Access Management
- Cyber Kill Chain
- Threat confidence and intelligence concepts
- CompTIA CySA+ study notes

[View Notes](Notes/)

---

## Skills Demonstrated

- Incident response
- Vulnerability prioritization
- MITRE ATT&CK analysis
- IOC classification
- Phishing analysis
- Authentication and authorization analysis
- Risk-based decision making
- Security-control evaluation
- Containment and eradication planning

---

## Practical Approach

The exercises focus on applying cybersecurity concepts to realistic scenarios rather than only defining terminology.

Typical workflow:

**Review Scenario → Identify Evidence → Assess Risk → Classify Activity → Determine Response**

All exercises were completed in **simulated cybersecurity training environments**.
