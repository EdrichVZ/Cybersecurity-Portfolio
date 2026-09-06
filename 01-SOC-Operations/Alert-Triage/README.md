# BASIC SOC Tier 1 Alert Triage & Incident Response Simulation

## Overview
This repository documents BASIC Security Operations Center (SOC) Level 1 alert triage and incident handling within a simulated SIEM environment. The primary focus is evaluating the alerts, executing alert prioritization workflows, gathering entity context, and determining appropriate incident verdicts without access to other tools and logs.

---

## Core SOC Triage Concepts

### Event vs. Alert
* **Raw System Event:** Routine activity logged by endpoints or network devices (e.g., standard user logins, process launches, or file writes).
* **Security Alert:** Flagged or correlated events triggered when system activity violates a detection rule or threshold requiring human analysis.

### Alert Classification
| Classification | Description | Required Action |
| :--- | :--- | :--- |
| **True Positive (TP)** | A genuine security threat or unauthorized activity occurred. | Immediate containment, host isolation, and escalation. |
| **False Positive (FP)** | Benign, normal activity incorrectly flagged by detection rules. | Alert closure and detection rule tuning. |
| **Benign Positive (BP)** | Suspicious behavior detected, but verified as authorized activity. | Verification with business unit and alert closure. |

---

## Triage & Prioritization Methodology

1. **Filter Unhandled Queue:** Exclude alerts already assigned or resolved by other analysts to prevent duplicate work.
2. **Prioritize by Severity:** Process queue items strictly by impact level: **Critical → High → Medium → Low**.
3. **Prioritize Dwell Time:** For alerts sharing the same severity level, triage the oldest timestamp first to minimize adversary dwell time.
4. **Context Gathering & Investigation:** Analyze core entities (IP, Host, User, File Hash) against threat intelligence and playbooks.
5. **Verdict & Remediation:** Document analyst notes, assign the final classification, and execute closure or L2 escalation.

---

## SIEM Investigation Case Studies (see SIEM_DASHBOARD screenshot)

### Case 1: Potential Data Exfiltration
* **Timestamp:** Mar 21st 2025 at 13:30
* **Severity:** Critical
* **Detection Rule:** 5+ GB transferred to a single destination in 24 hours
* **Entities Involved:**
  * **Source Host:** `192.168.45.66` (`UK04/MEETINGROOM`)
  * **Destination:** `*.zoom.us`
  * **Data Metrics:** 5.8 GB Sent / 5.2 GB Received

> **Verdict: False Positive**  
> **Analyst Notes:** The host is a designated conference room system (`UK04/MEETINGROOM`). Large bidirectional data transfers to verified Zoom domains represent legitimate video conferencing activity during an extended meeting. Recommended tuning rule thresholds for conference room network segments.

---

### Case 2: Double-Extension File Creation
* **Timestamp:** Mar 21st 2025 at 13:58
* **Severity:** High
* **Detection Rule:** Creation of double-extension executables (`*.mp4.exe`, `*.pdf.exe`)
* **Entities Involved:**
  * **Host / User:** `LPT-HR-009` / `S.Conway`
  * **Process Name:** `chrome.exe`
  * **Target File:** `C:\Users\S.Conway\Downloads\cats2025.mp4.exe`
  * **Download Source:** `https://freecatvideoshd.monster/cats2025.mp4.exe`
  * **File Hash (MD5):** `14d8486f3f63875ef93cfd240c5dc10b`

> **Verdict: True Positive**  
> **Analyst Notes:** User downloaded a malicious executable disguised as a video file via Google Chrome. Threat intelligence scanning confirmed the domain and MD5 hash are malicious. Escalated to Tier 2 for host isolation, system scanning, and execution log analysis.

---

### Case 3: Download from GitHub Repository
* **Timestamp:** Mar 21st 2025 at 13:02
* **Severity:** Low
* **Detection Rule:** File or repository download from GitHub
* **Entities Involved:**
  * **Host / User:** `LPT-IT-063` / `G.Chandler`
  * **Network Segment:** `VPN/DEVELOPERS`
  * **Accessed URL:** `https://github.com/facebook/react`

> **Verdict: False Positive**  
> **Analyst Notes:** The activity originated from a known IT Developer account accessing a legitimate, mainstream open-source repository (`facebook/react`). No malicious payloads or unauthorized tools were involved. Closed as False Positive.

---

## Key Takeaways & Skills Demonstrated
* Mastered L1 queue management and severity-based prioritization.
* Evaluated web artifacts, Mark-of-the-Web (MotW) URLs, and MD5 file hashes against threat intelligence sources.
* Performed correlation between user roles, host locations, and baseline behavior to eliminate false alarms.
* Practiced incident documentation and L2 escalation handoffs.
