# Windows Threat Detection

This section documents practical **Windows threat detection and endpoint investigation** using Windows Security Event Logs and Sysmon telemetry.

The labs focus on identifying attacker activity across multiple stages of an intrusion, from initial access through discovery, persistence, Command and Control, and post-compromise behavior.

The goal is to understand how a SOC analyst can use Windows telemetry to identify suspicious activity, correlate related events, and determine whether further investigation or escalation is required.

---

## Labs

| Lab | Focus | Link |
| :--- | :--- | :--- |
| **Windows Threat Detection 1** | Initial Access, authentication activity, phishing, suspicious files, and process creation. | [View Lab](Threat-Detection-1/) |
| **Windows Threat Detection 2** | Post-compromise activity, discovery, process analysis, data staging, and tool transfer. | [View Lab](Threat-Detection-2/) |
| **Windows Threat Detection 3** | Persistence, Command and Control, account activity, scheduled tasks, services, and registry persistence. | [View Lab](Threat-Detection-3/) |

---

## Windows Threat Detection 1 — Initial Access

This lab focuses on identifying early signs of compromise using Windows Security Event Logs and Sysmon telemetry.

Investigation topics include:

- Successful and failed logon activity
- RDP authentication
- Brute-force indicators
- Suspicious file activity
- Phishing-related behavior
- Process creation
- Removable-media activity
- Basic alert triage
- MITRE ATT&CK mapping

Windows events reviewed include:

- **Event ID 4624** — Successful Logon
- **Event ID 4625** — Failed Logon
- **Sysmon Event ID 1** — Process Creation
- **Sysmon Event ID 11** — File Creation

[View Windows Threat Detection 1](Threat-Detection-1/)

---

## Windows Threat Detection 2 — Post-Compromise Activity

This lab focuses on identifying attacker behavior after initial access has already been achieved.

The investigation emphasizes process relationships, command-line activity, system discovery, and data preparation.

Topics include:

- Process-tree analysis
- Parent/child process relationships
- Command-line investigation
- Account discovery
- File and directory discovery
- Process discovery
- Security-tool discovery
- Credential-related file discovery
- Clipboard activity
- Data staging
- Suspicious network activity
- Ingress tool transfer
- MITRE ATT&CK mapping

A key focus is understanding how apparently legitimate Windows utilities can become suspicious when viewed in the context of a larger attack sequence.

[View Windows Threat Detection 2](Threat-Detection-2/)

---

## Windows Threat Detection 3 — Persistence & Command and Control

This lab focuses on identifying techniques used by attackers to maintain access to compromised Windows systems and communicate with external infrastructure.

Investigation topics include:

- Command-and-Control activity
- Suspicious network connections
- Repeated beaconing behavior
- Account creation
- Privilege-related activity
- Windows service abuse
- Scheduled-task persistence
- Registry Run Key persistence
- Startup-folder persistence
- Process-tree investigation
- MITRE ATT&CK mapping

The lab demonstrates how multiple persistence mechanisms and network indicators can be correlated to identify continuing attacker access.

[View Windows Threat Detection 3](Threat-Detection-3/)

---

## Skills Demonstrated

- Windows Event Log analysis
- Sysmon analysis
- Authentication investigation
- RDP investigation
- Process-tree analysis
- Parent/child process analysis
- Command-line investigation
- Persistence detection
- Scheduled-task analysis
- Registry persistence analysis
- Windows service investigation
- Command-and-Control identification
- Network activity investigation
- MITRE ATT&CK mapping
- Endpoint threat detection

---

## Data Sources

- **Windows Security Event Logs**
- **Sysmon telemetry**
- Authentication events
- Process-creation events
- File-creation events
- Network telemetry
- Windows service activity
- Registry activity
- Scheduled-task activity

---

## Investigation Approach

The labs follow a practical endpoint-investigation workflow:

**Event Review → Process Analysis → Context Validation → Behavior Correlation → Threat Identification → Escalation Decision**

All investigations were completed in **simulated cybersecurity training environments**.
