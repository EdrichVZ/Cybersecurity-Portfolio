# Cybersecurity Portfolio

A hands-on cybersecurity portfolio focused on the practical skills required for a **Junior SOC Analyst / Tier 1 Security Analyst** role.

This repository documents security investigations, alert triage, phishing analysis, threat hunting, SIEM investigations, network traffic analysis, Windows threat detection, and Linux threat detection performed in simulated environments.

The primary focus of this portfolio is not simply completing labs, but demonstrating the ability to **analyze security events, correlate evidence, distinguish true positives from false positives, investigate attack activity, and recommend appropriate containment or escalation actions**.

---

## Core Areas

- **SOC Alert Triage & Incident Investigation**
- **SIEM & Log Analysis — Splunk**
- **Phishing Investigation**
- **Threat Hunting**
- **Network Traffic Analysis — Wireshark**
- **Windows Event Log & Sysmon Analysis**
- **Linux Threat Detection & auditd Analysis**
- **MITRE ATT&CK**
- **IOC Analysis**
- **Incident Response**
- **Vulnerability Prioritization**

---

## Featured Investigations

### SOC Alert Triage & Attack Correlation

Investigation of simulated SOC alerts requiring **True Positive / False Positive classification, evidence correlation, escalation decisions, and remediation recommendations**.

The challenge investigations include multi-stage attack activity involving:

- PowerShell execution and persistence
- Command and Control (C2) communication
- VPN and SSH brute-force attacks
- Successful account compromise
- Network reconnaissance and lateral movement
- Web application attacks and web shell deployment
- Linux command execution and suspicious DNS activity

**Skills:** Alert Triage · Incident Analysis · Alert Correlation · Escalation · Windows · Linux · Network Security

[View SOC Alert Triage Challenges](01-SOC-Investigations/Alert-Triage-Challenges/)

---

### Splunk — Compromised Windows Host Investigation

A SOC investigation using **Splunk and Windows Event Logs** to analyze suspicious activity on a compromised Windows workstation.

The investigation identified:

- An impersonation account
- Suspicious scheduled-task activity
- Windows process execution
- Abuse of `certutil.exe`
- External payload retrieval
- Suspicious executable execution
- Indicators of host compromise

The investigation demonstrates the use of SIEM searches and event correlation to reconstruct suspicious endpoint activity.

**Tools:** Splunk · Windows Event Logs · Event ID 4688 · Process Analysis

[View Splunk Investigations](03-Splunk-Investigations/)

---

### Phishing & Credential Compromise Investigations

Multiple phishing investigations covering malicious attachments, credential harvesting, spoofed domains, compromised accounts, and post-compromise activity.

Investigations include analysis of:

- SPF, DKIM and DMARC failures
- Domain spoofing and typosquatting
- Malicious attachments and links
- Credential-harvesting infrastructure
- PowerShell execution
- Network-share discovery
- Data staging
- DNS tunnelling and exfiltration
- IOC extraction and containment recommendations

**Skills:** Phishing Analysis · Email Security · IOC Analysis · Incident Response · MITRE ATT&CK

[View Phishing Investigations](01-SOC-Investigations/Phishing-Investigations/)

---

### Threat Hunting — Ransomware Intrusion

A threat-hunting investigation reconstructing a simulated ransomware intrusion using endpoint and network telemetry.

The investigation follows attacker activity across multiple stages including:

- Initial compromise
- Malicious process execution
- PowerShell activity
- Credential theft
- Active Directory enumeration
- Credential reuse
- Remote access
- Lateral movement
- Privilege escalation
- Persistence
- Ransomware execution

**Skills:** Threat Hunting · Event Correlation · Windows Security · Active Directory · Attack Reconstruction

[View Threat Hunting Investigation](01-SOC-Investigations/Threat-Hunting/Typo%20Snare%20Scenario/)

---

## Additional Practical Labs

| Area | Description | Link |
| :--- | :--- | :--- |
| **SOC Practical Exercises** | Scenario-based exercises covering incident response, vulnerability prioritization, MITRE ATT&CK, IOC analysis, phishing, and security controls. | [View Exercises](02-Practical-Exercises/) |
| **Wireshark Practice** | PCAP and network traffic analysis covering protocols, reconnaissance, suspicious traffic, tunnelling, and web activity. | [View Wireshark Labs](04-Wireshark-Practice/) |
| **Windows Threat Detection** | Windows Security Event Log and Sysmon exercises covering Initial Access, Discovery, Persistence, Command and Control, and post-compromise activity. | [View Windows Labs](05-Windows-Threat-Detection/) |

---

## Tools & Technologies

### SIEM & Security Monitoring

- Splunk
- Microsoft Sentinel
- Windows Event Logs
- Sysmon

### Network Analysis

- Wireshark
- PCAP / PCAPNG analysis
- TCP/IP
- DNS
- HTTP / HTTPS
- SMB
- RDP
- SSH

### Endpoint & Operating Systems

- Windows
- Linux
- PowerShell
- Windows Registry
- Scheduled Tasks
- Windows Services

### Security Concepts & Frameworks

- MITRE ATT&CK
- Cyber Kill Chain
- Indicators of Compromise (IOCs)
- CVSS
- CISA Known Exploited Vulnerabilities (KEV)
- Incident Response
- Threat Hunting
- Vulnerability Prioritization

### Identity & Access

- Active Directory
- Authentication and Authorization
- Multi-Factor Authentication
- Role-Based Access Control
- Privileged Access Management

---

## Investigation Skills Demonstrated

Throughout the portfolio, investigations focus on practical SOC workflows such as:

- Reviewing security alerts and determining whether activity is malicious
- Distinguishing True Positives from False Positives
- Correlating multiple alerts into larger attack sequences
- Analyzing Windows processes and parent/child relationships
- Investigating suspicious authentication activity
- Identifying persistence mechanisms
- Detecting C2 communication and suspicious network behavior
- Analyzing phishing indicators and email authentication failures
- Extracting and documenting Indicators of Compromise
- Mapping attacker activity to MITRE ATT&CK
- Recommending containment, eradication, and escalation actions
- Documenting findings in structured incident reports

---

## Repository Structure

```text
Cybersecurity-Portfolio/
│
├── 01-SOC-Investigations/
│   ├── Alert-Triage-Challenges/
│   ├── Phishing-Investigations/
│   └── Threat-Hunting/
│
├── 02-Practical-Exercises/
│   ├── Exercises/
│   └── Notes/
│
├── 03-Splunk-Investigations/
│
├── 04-Wireshark-Practice/
│
├── 05-Windows-Threat-Detection/
│
└── README.md
```

---

## Planned Projects

Future additions to this portfolio will expand into additional SOC and defensive security areas.

### Linux Threat Investigations

Planned investigations focused on Linux security telemetry and common post-compromise activity, including:

- Authentication attacks
- Suspicious process execution
- Bash and shell activity
- Privilege escalation
- Persistence mechanisms
- Suspicious cron jobs and services
- Network connections
- Log analysis and incident reconstruction

### Active Directory Threat Detection

Planned investigations covering identity-based attacks in Windows domain environments, including:

- Suspicious authentication activity
- Account enumeration
- Credential abuse
- Kerberos-related activity
- Lateral movement
- Privilege escalation
- Administrative account misuse
- Active Directory attack detection

### Microsoft Sentinel Investigations

Additional SIEM investigations using Microsoft Sentinel, with emphasis on:

- KQL-based log analysis
- Alert triage
- Incident correlation
- Entity investigation
- Authentication analysis
- Threat hunting
- Incident response workflows

### Expanded Network Threat Hunting

Additional packet-level investigations involving:

- Command and Control traffic
- Data exfiltration
- DNS and ICMP tunnelling
- Lateral movement
- Suspicious protocol usage
- Attack reconstruction from PCAP data

These projects will be added as they are completed and documented.

---

## Portfolio Focus

This portfolio is continuously developed as part of my progression toward a **SOC Analyst / Cybersecurity Analyst** role.

The projects are designed to demonstrate practical investigation ability rather than only theoretical knowledge, with emphasis on:

**Triage → Investigation → Correlation → Classification → Escalation → Remediation**

All environments and incidents documented in this repository are **simulated training environments** used for cybersecurity practice and skills development.
