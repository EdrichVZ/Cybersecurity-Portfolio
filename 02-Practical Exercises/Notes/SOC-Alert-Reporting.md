# BASIC SOC Tier 1 Alert Report Writing 5 W's

## Overview
This repository documents Security Operations Center (SOC) Tier 1 alert reporting methodologies, incident documentation standards, and escalation communication protocols. It highlights the practical application of the **5 W's Framework** (Who, What, When, Where, Why) to structure investigative findings for Tier 2 (L2) and Digital Forensics and Incident Response (DFIR) handoffs.

---

## The 5 W's Reporting Framework
Structured reporting ensures key investigative context is communicated without ambiguity:

* **Who:** Identifies the target user, compromised account, or execution authority.
* **What:** Summarizes the specific actions, command sequences, or payload deliveries.
* **When:** Captures the exact UTC timestamp of the activity.
* **Where:** Pinpoints the affected endpoint host, IP address, or application mailbox.
* **Why:** Details the technical justification, authentication failures, or process anomalies supporting the final verdict.

---

## Escalation Criteria & Operational Protocols

### When to Escalate to L2 / DFIR
* **Active Exploitation:** Confirmed attacks requiring immediate host isolation or account containment.
* **Remediation Needs:** Security events demanding password resets or network policy changes.
* **High-Risk Scope:** Incidents involving executive staff or critical infrastructure assets.
* **Triage Ambiguity:** Complex logs or parsing gaps that prevent conclusive Tier 1 analysis.

### SOC Communication Edge Cases
* **Unresponsive L2 Analyst:** Escalate through emergency contacts in sequence: L2 Analyst $\rightarrow$ L3 Analyst $\rightarrow$ SOC Manager.
* **Compromised Communication Channels:** If Slack/Teams account compromise is suspected, verify activity with the user out-of-band (e.g., direct phone call).
* **High Alert Spikes:** Maintain strict severity prioritization and notify the L2 on shift regarding queue volume.
* **Unparsed SIEM Logs:** Perform manual inspection on raw log fields rather than skipping the alert, and report the parsing error to SOC engineering.

---

## SIEM Investigation Case Studies (see SIEM_Dashboard screenshot)

### Case 1: Email Marked as Phishing after Delivery (see Alert1_Info and Alert1-Repsonse screenshots)
* **Severity:** Medium
* **Verdict:** True Positive
* **Status:** In Progress / Escalated

> **5 W's Incident Summary:**
> * **Who:** Sender: `support@microsoft.com` (Spoofed display name: Microsoft Support); Recipient: Eddie Huffman, IT Manager (`e.huffman@tryhackme.thm`).
> * **What:** Automated post-delivery classification flagged a phishing email containing an urgent price-increase notice and a suspicious compressed attachment (`REPORT.rar`).
> * **When:** March 27, 2025, at 19:25 UTC.
> * **Where:** Recipient email inbox (`e.huffman@tryhackme.thm`).
> * **Why:** The email failed authentication checks (SPF: Fail, DKIM: Fail), indicating domain spoofing. The body uses high-pressure social engineering language ("600% price increase", "urgent notice") and delivers a compressed RAR file capable of staging malicious payloads.

---

### Case 2: Spike of Domain Discovery Commands (see Alert2_Info and Alert2-Repsonse screenshots)
* **Severity:** Medium
* **Verdict:** True Positive
* **Status:** In Progress / Escalated

> **5 W's Incident Summary:**
> * **Who:** `NT AUTHORITY\SYSTEM` (Executed via parent process `revshell.exe`).
> * **What:** Execution of active domain discovery commands (`dir`, `hostname`, `whoami /priv`, `net group "Domain Admins" /domain`, `nltest /dclist:tryhackme.thm`) via `cmd.exe`.
> * **When:** March 27, 2025, at 19:56 UTC.
> * **Where:** Host `DMZ-MSEXCHANGE-2013` (Windows Server 2012 R2).
> * **Why:** Process lineage (`w3wp.exe` $\rightarrow$ `revshell.exe` $\rightarrow$ `cmd.exe`) indicates web server exploitation. An attacker is executing enumeration commands under elevated privileges to map Domain Controllers and Domain Admins for lateral movement.
