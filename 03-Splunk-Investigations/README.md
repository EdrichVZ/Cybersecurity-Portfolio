# Splunk Investigations

This section documents hands-on **SIEM investigation and log-analysis practice using Splunk**.

The investigations simulate common SOC workflows involving alert validation, Windows Event Log analysis, authentication activity, persistence mechanisms, web attacks, process execution, and compromised endpoint investigation.

The focus is on using Splunk to **search and correlate security events, identify suspicious behavior, determine incident scope, and document investigative findings**.

---

## Investigations

| Investigation | Focus | Link |
| :--- | :--- | :--- |
| **Successful Brute-Force Attack** | Authentication analysis, failed and successful logins, source-IP investigation, and account compromise. | [View Report](Scenario%201%20-%20Reports/01-Initial%20Access%20Alert/01-REPORT.md) |
| **Scheduled Task Persistence** | Windows Event ID 4698, scheduled-task creation, execution context, and persistence analysis. | [View Report](Scenario%201%20-%20Reports/02-Persistence%20Alert/02-REPORT.md) |
| **Web Shell Investigation** | Web attack investigation, threat-intelligence enrichment, brute-force activity, and web-shell indicators. | [View Report](Scenario%201%20-%20Reports/03-Web%20Shell%20Alert/03-REPORT.md) |
| **Compromised Windows Host** | Process creation, suspicious accounts, LOLBin abuse, payload retrieval, and endpoint compromise reconstruction. | [View Report](Scenario%202%20-%20Report/Report.md) |

---

## Featured Investigation

### Compromised Windows Host

A simulated SOC investigation into suspicious activity affecting a Windows workstation in an organization's HR department.

The investigation used **Splunk and Windows Event ID 4688 process-creation logs** to analyze activity across the affected endpoint.

Initial review identified approximately **13,959 Windows events** within the investigation dataset.

The investigation uncovered multiple indicators of compromise, including:

- An impersonation account resembling a legitimate user
- Suspicious scheduled-task activity
- Abnormal Windows process execution
- Use of the LOLBin `certutil.exe`
- Retrieval of a payload from external infrastructure
- Suspicious network communication
- Download and execution of a suspicious executable

The available evidence was correlated to reconstruct the sequence of activity and determine that the affected Windows endpoint had been compromised.

[View Compromised Windows Host Investigation](Scenario%202%20-%20Report/Report.md)

---

## Scenario 1 — Alert Investigations

### Successful Brute-Force Attack

Authentication events were investigated to determine whether repeated failed login attempts resulted in successful account access.

The investigation included:

- Failed authentication analysis
- Successful login identification
- Source-IP investigation
- User-account analysis
- Event correlation
- Compromise determination
- Escalation decision

[View Investigation](Scenario%201%20-%20Reports/01-Initial%20Access%20Alert/01-REPORT.md)

---

### Scheduled Task Persistence

Investigation of suspicious scheduled-task creation on a Windows endpoint.

The investigation focused on:

- Windows Event ID **4698**
- Task creation
- User context
- Execution triggers
- Scheduled-task actions
- Persistence indicators

[View Investigation](Scenario%201%20-%20Reports/02-Persistence%20Alert/02-REPORT.md)

---

### Web Shell Investigation

Investigation of suspicious activity targeting a web server.

The analysis included:

- Suspicious source-IP investigation
- Threat-intelligence enrichment
- Web-server log analysis
- Automated brute-force activity
- Suspicious HTTP requests
- Potential web-shell deployment
- Escalation assessment

[View Investigation](Scenario%201%20-%20Reports/03-Web%20Shell%20Alert/03-REPORT.md)

---

## Skills Demonstrated

- Splunk search and investigation
- SIEM alert analysis
- Windows Event Log analysis
- Authentication investigation
- Process-creation analysis
- Event correlation
- Scheduled-task persistence detection
- LOLBin identification
- Web attack investigation
- Threat-intelligence enrichment
- IOC identification
- Incident classification
- Escalation decision making
- Incident report writing

---

## Tools & Data Sources

- **Splunk**
- **Windows Event Logs**
- **Windows Event ID 4688 — Process Creation**
- **Windows Event ID 4698 — Scheduled Task Creation**
- Web-server logs
- Authentication logs
- Threat-intelligence sources

---

## Investigation Workflow

The investigations generally follow a structured SOC workflow:

**Alert Review → Splunk Search → Event Analysis → Evidence Correlation → Classification → Escalation Decision → Documentation**

All investigations were completed in **simulated cybersecurity training environments**.
