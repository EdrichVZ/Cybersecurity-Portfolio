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

### 1. Phishing Distribution & Lure Analysis

Initial triage focused on identifying affected mailboxes and analyzing incoming lure messages across impacted corporate departments.

* **Target Recipients:** `Derick Marshall`, `Michael Ascot`, `Michelle Chen`, `Zoe Duncan`, `William McClean`
* **Target Email Addresses:** `derick.marshall@swiftspend.finance`, `michael.ascot@swiftspend.finance`, `michelle.chen@swiftspend.finance`, `zoe.duncan@swiftspend.finance`, `william.mcclean@swiftspend.finance`
* **Adversary Sender Address:** `Accounts.Payable@groupmarketingonline.icu`
* **Suspected Malicious Attachments:** `Direct Credit Advice.html` (approx. 515 bytes) and `Quote.pdf` (107 KB)
* **Delivery Mechanism:** Spear-phishing blast delivered on June 29, 2020, at 06:01 AM, utilizing HTML attachments and a malicious PDF containing embedded redirection URLs.

**Notes:**  
The adversary utilized `groupmarketingonline.icu` to send targeted lures across multiple departments, using business-themed subjects (e.g., "Quote for Services Rendered" and "Direct Credit Advice") to induce compliance. The presence of identical timestamps (06:01) across multiple recipients indicates an automated script or mailer tool was used to execute the campaign simultaneously.

---
