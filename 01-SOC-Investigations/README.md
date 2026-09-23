# SOC Investigations

This section contains hands-on SOC investigations focused on **alert triage, incident analysis, phishing investigation, threat hunting, attack correlation, and escalation decisions**.

The scenarios simulate common Tier 1 SOC responsibilities, including reviewing security alerts, validating evidence, distinguishing malicious activity from benign behavior, correlating related events, and documenting appropriate response actions.

---

## Investigation Areas

| Area | Description | Link |
| :--- | :--- | :--- |
| **Alert Triage Challenges** | Multi-alert SOC investigations requiring True Positive / False Positive classification, correlation, escalation decisions, and remediation recommendations. | [View Challenges](Alert-Triage-Challenges/) |
| **Phishing Investigations** | Phishing and credential-compromise investigations covering malicious attachments, spoofing, credential harvesting, DNS tunnelling, and IOC extraction. | [View Investigations](Phishing-Investigations/) |
| **Threat Hunting** | Threat-hunting investigation reconstructing a ransomware intrusion across endpoint and network telemetry. | [View Threat Hunt](Threat-Hunting/Typo%20Snare%20Scenario/) |

---

## Alert Triage Challenges

Four simulated SOC challenge environments were investigated using host, network, authentication, process, and business context.

### Challenge 1 — Windows Compromise & Persistence

Investigation of a multi-stage Windows compromise involving:

- Suspicious PowerShell execution
- External Command-and-Control communication
- Persistence account creation
- External RDP access
- Interactive command execution
- Volume Shadow Copy deletion
- True Positive / False Positive classification
- Escalation and remediation decisions

[View Challenge 1](Alert-Triage-Challenges/SOC-Challenge%201/)

---

### Challenge 2 — VPN & SSH Authentication Attacks

Investigation of authentication attacks involving:

- VPN brute-force attempts
- Successful account compromise
- Internal network reconnaissance
- Possible lateral movement
- SSH brute-force activity
- Firewall and network traffic analysis
- Legitimate administrative traffic identification

[View Challenge 2](Alert-Triage-Challenges/SOC-Challenge%202/)

---

### Challenge 3 — Web Server Compromise

Investigation of a web attack progressing through multiple stages:

- Directory and file enumeration
- Administrative portal brute force
- Successful authentication
- Web-shell upload
- Arbitrary command execution
- Unauthorized file modification
- SSH brute-force activity
- False Positive validation using network context

[View Challenge 3](Alert-Triage-Challenges/SOC-Challenge%203/)

---

### Challenge 4 — Linux & DNS Activity

Linux-focused alert investigation covering:

- Suspicious command execution
- External payload download
- File permission modification
- Privileged activity
- Suspicious DNS queries
- Potential Command-and-Control traffic
- Legitimate system-administration activity
- Security-tool website access
- Phishing-related alerts

[View Challenge 4](Alert-Triage-Challenges/SOC-Challenge%204/)

---

## Phishing Investigations

### Scenario 1 — Malicious Attachment & Artifact Analysis

Investigation of a targeted phishing email involving:

- Suspicious sender behavior
- Domain spoofing
- Malicious attachment analysis
- Deceptive file extensions
- IOC extraction
- Containment recommendations

[View Scenario 1](Phishing-Investigations/Scenario%201/)

---

### Scenario 2 — Account Compromise & DNS Exfiltration

Large-scale alert investigation covering **36 security cases** involving both malicious and benign activity.

Attack activity included:

- Phishing-based initial compromise
- PowerShell execution
- Network-share discovery
- Data staging
- Evidence cleanup
- DNS tunnelling
- Data exfiltration
- MITRE ATT&CK mapping
- Containment and eradication recommendations

The investigation required analysts to distinguish **15 True Positives from 21 False Positives** while correlating events into a larger compromise sequence.

[View Scenario 2](Phishing-Investigations/Scenario%202/)

---

### Scenario 3 — Credential Harvesting

Investigation of an active phishing campaign involving:

- Domain spoofing
- Malicious PDF links
- Credential-harvesting infrastructure
- Compromised user credentials
- Phishing-kit analysis
- IOC identification
- Containment and remediation

[View Scenario 3](Phishing-Investigations/Scenario%203/)

---

## Threat Hunting

### Typo Snare — Ransomware Intrusion

Threat-hunting investigation reconstructing attacker activity across endpoint and network telemetry.

The investigation follows activity including:

- Suspicious network connections
- Process creation
- Malicious file execution
- PowerShell activity
- Credential theft
- Active Directory enumeration
- Credential reuse
- Remote access
- Lateral movement
- Privilege escalation
- Persistence
- Ransomware execution

[View Threat Hunting Investigation](Threat-Hunting/Typo%20Snare%20Scenario/)

---

## Skills Demonstrated

- SOC alert triage
- True Positive / False Positive classification
- Alert correlation
- Incident escalation
- Windows and Linux event analysis
- Authentication investigation
- Process and parent/child process analysis
- PowerShell investigation
- Command-and-Control identification
- Persistence detection
- Phishing analysis
- IOC identification
- Threat hunting
- MITRE ATT&CK mapping
- Network activity analysis
- Incident containment and remediation planning

---

## Investigation Approach

The investigations in this section generally follow a structured SOC workflow:

**Alert Review → Evidence Collection → Context Validation → Correlation → Classification → Escalation Decision → Recommended Response**

All investigations were performed in **simulated cybersecurity training environments**.
