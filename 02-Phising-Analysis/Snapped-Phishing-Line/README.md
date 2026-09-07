# Incident Analysis Report: Phishing & Credential Harvesting Investigation

**Incident ID:** INC-2026-0914  
**Severity:** High  
**Status:** In Progress / Containment  
**Target:** SwiftSpend Financial  

---

## Scenario Summary

On September 14, 2026, the Security Operations Center (SOC) initiated an incident response workflow following reports from multiple employees regarding suspicious emails. Several users reported receiving an unsolicited invoice quote, and subsequent investigation confirmed that credentials were submitted to an adversary-controlled landing page, resulting in compromised user access.

Technical analysis confirmed an active spear-phishing campaign employing domain spoofing, embedded PDF links, and a hosted credential-harvesting kit (**Update365**). The open directory configuration on the threat actor's web infrastructure enabled the recovery of the phishing kit source code and log files. Key Indicators of Compromise (IOCs) and exfiltration channels have been documented for immediate remediation.

---

## Technical Investigation & Artifact Extraction

### 1. Phishing Distribution & Lure Analysis

Initial triage focused on identifying affected mailboxes and analyzing incoming lure messages.

* **Target Recipient (Quote for Services Rendered):** `William McClean`
* **Adversary Sender Address:** `Accounts.Payable@groupmarketingonline.icu`
* **Target Recipient (Attachment Vector):** `Zoe Duncan`
* **Delivery Mechanism:** Malicious PDF containing embedded redirection URLs.

> **Analyst Notes:**  
> The adversary utilized `groupmarketingonline.icu` to send targeted lures across multiple departments, using business-themed lures (e.g., "Quote for Services Rendered") to induce compliance.

---
