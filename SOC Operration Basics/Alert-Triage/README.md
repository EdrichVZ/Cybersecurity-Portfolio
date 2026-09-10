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

### Case 1: Potential Data Exfiltration (see Alert1_Info and Alert1_Response screenshots)
* **Timestamp:** Mar 21st 2025 at 13:30
* **Severity:** Critical
* **Detection Rule:** 5+ GB transferred to a single destination in 24 hours
* **Entities Involved:**
  * **Source Host:** `192.168.45.66` (`UK04/MEETINGROOM`)
  * **Destination:** `*.zoom.us`
  * **Data Metrics:** 5.8 GB Sent / 5.2 GB Received

> **Verdict: False Positive**  
> **Analyst Notes:** At Mar 21st 2025 13:30 a potential data exfiltration alert was triggered. Considering the available information at this time, this is a False Positive. The source IP (192.168.45.66) is a known internal host device located in UK04/MEETINGROOM. The data being sent (5.8GB) and received(5.2GB) to and from *.zoom.us (non-malicious source) is normal behaviour for a video conference/meeting call. The conference/meeting call most likely lasted longer then expected resulting in this alert being triggered. Alert rules might need to be updated to allow longer meetings/conference calls.

---

### Case 2: Double-Extension File Creation (see Alert2_Info and Alert2_Response screenshots)
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
> **Analyst Notes:** Mar 21st 2025 at 13:58 a double-extension file alert was triggered. Based on the information present, this is a True Positive. The Host: LPT-HR-009 user: S.Conway has downloaded a called cats2025.mp4.exe (C:\Users\S.Conway\Downloads\cats2025.mp4.exe) via chrome.exe from https://freecatvideoshd.monster/cats2025.mp4.exe. Scanning has revealed the domain and file MD5(14d8486f3f63875ef93cfd240c5dc10b) is malicious. Escalating to T2 agent device might be compromised if user has open the malicious file. Suggest removing the file and scanning host system. Host isolation and log investigation necessary if file was executed.

---

### Case 3: Download from GitHub Repository (see Alert3_Info and Alert3_Response screenshots)
* **Timestamp:** Mar 21st 2025 at 13:02
* **Severity:** Low
* **Detection Rule:** File or repository download from GitHub
* **Entities Involved:**
  * **Host / User:** `LPT-IT-063` / `G.Chandler`
  * **Network Segment:** `VPN/DEVELOPERS`
  * **Accessed URL:** `https://github.com/facebook/react`

> **Verdict: False Positive**  
> **Analyst Notes:** At Mar 21st 2025 at 13:02 a GitHub download alert was triggered. This is a False Positive, user G.Chandler and host LPT-IT-063 is from the IT Developers department. The URL https://github.com/facebook/react which the download is from is legitimate, and no evidence of Malicious intent from the download has been present.

---

## Key Takeaways & Skills Demonstrated
* Mastered L1 queue management and severity-based prioritization.
* Evaluated web artifacts, Mark-of-the-Web (MotW) URLs, and MD5 file hashes against threat intelligence sources.
* Performed correlation between user roles, host locations, and baseline behavior to eliminate false alarms.
* Practiced incident documentation and L2 escalation handoffs.
