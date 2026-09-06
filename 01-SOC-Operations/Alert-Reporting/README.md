# SOC Tier 1 Alert Reporting & Escalation Framework

## Overview
This repository documents Security Operations Center (SOC) Tier 1 alert reporting methodologies, incident documentation standards, and escalation communication protocols[cite: 2]. It highlights the practical application of the **5 W's Framework** (Who, What, When, Where, Why) to structure investigative findings for Tier 2 (L2) and Digital Forensics and Incident Response (DFIR) handoffs[cite: 2].

---

## The 5 W's Reporting Framework
Structured reporting ensures key investigative context is communicated without ambiguity[cite: 2]:

* **Who:** Identifies the target user, compromised account, or execution authority[cite: 2].
* **What:** Summarizes the specific actions, command sequences, or payload deliveries[cite: 2].
* **When:** Captures the exact UTC timestamp of the activity[cite: 2].
* **Where:** Pinpoints the affected endpoint host, IP address, or application mailbox[cite: 2].
* **Why:** Details the technical justification, authentication failures, or process anomalies supporting the final verdict[cite: 2].

---

## Escalation Criteria & Operational Protocols

### When to Escalate to L2 / DFIR
* **Active Exploitation:** Confirmed attacks requiring immediate host isolation or account containment[cite: 2].
* **Remediation Needs:** Security events demanding password resets or network policy changes[cite: 2].
* **High-Risk Scope:** Incidents involving executive staff or critical infrastructure assets[cite: 2].
* **Triage Ambiguity:** Complex logs or parsing gaps that prevent conclusive Tier 1 analysis[cite: 2].

### SOC Communication Edge Cases
* **Unresponsive L2 Analyst:** Escalate through emergency contacts in sequence: L2 Analyst $\rightarrow$ L3 Analyst $\rightarrow$ SOC Manager[cite: 2].
* **Compromised Communication Channels:** If Slack/Teams account compromise is suspected, verify activity with the user out-of-band (e.g., direct phone call)[cite: 2].
* **High Alert Spikes:** Maintain strict severity prioritization and notify the L2 on shift regarding queue volume[cite: 2].
* **Unparsed SIEM Logs:** Perform manual inspection on raw log fields rather than skipping the alert, and report the parsing error to SOC engineering[cite: 2].

---

## SIEM Investigation Case Studies

### Case 1: Email Marked as Phishing after Delivery
* **Severity:** Medium[cite: 2]
* **Verdict:** True Positive[cite: 2]
* **Status:** In Progress / Escalated[cite: 2]

> **5 W's Incident Summary:**
> * **Who:** Sender: `support@microsoft.com` (Spoofed display name: Microsoft Support); Recipient: Eddie Huffman, IT Manager (`e.huffman@tryhackme.thm`)[cite: 2].
> * **What:** Automated post-delivery classification flagged a phishing email containing an urgent price-increase notice and a suspicious compressed attachment (`REPORT.rar`)[cite: 2].
> * **When:** March 27, 2025, at 19:25 UTC[cite: 2].
> * **Where:** Recipient email inbox (`e.huffman@tryhackme.thm`)[cite: 2].
> * **Why:** The email failed authentication checks (SPF: Fail, DKIM: Fail), indicating domain spoofing[cite: 2]. The body uses high-pressure social engineering language ("600% price increase", "urgent notice") and delivers a compressed RAR file capable of staging malicious payloads[cite: 2].

---

### Case 2: Spike of Domain Discovery Commands
* **Severity:** Medium[cite: 2]
* **Verdict:** True Positive[cite: 2]
* **Status:** In Progress / Escalated[cite: 2]

> **5 W's Incident Summary:**
> * **Who:** `NT AUTHORITY\SYSTEM` (Executed via parent process `revshell.exe`)[cite: 2].
> * **What:** Execution of active domain discovery commands (`dir`, `hostname`, `whoami /priv`, `net group "Domain Admins" /domain`, `nltest /dclist:tryhackme.thm`) via `cmd.exe`[cite: 2].
> * **When:** March 27, 2025, at 19:56 UTC[cite: 2].
> * **Where:** Host `DMZ-MSEXCHANGE-2013` (Windows Server 2012 R2)[cite: 2].
> * **Why:** Process lineage (`w3wp.exe` $\rightarrow$ `revshell.exe` $\rightarrow$ `cmd.exe`) indicates web server exploitation[cite: 2]. An attacker is executing enumeration commands under elevated privileges to map Domain Controllers and Domain Admins for lateral movement[cite: 2].
