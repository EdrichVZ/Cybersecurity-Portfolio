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

### 1. Phishing Distribution & Lure Analysis (see 01_All_Emails screenshot)

Initial triage focused on identifying affected mailboxes and analyzing incoming lure messages across impacted corporate departments.

* **Target Recipients:** `Derick Marshall`, `Michael Ascot`, `Michelle Chen`, `Zoe Duncan`, `William McClean`
* **Target Email Addresses:** `derick.marshall@swiftspend.finance`, `michael.ascot@swiftspend.finance`, `michelle.chen@swiftspend.finance`, `zoe.duncan@swiftspend.finance`, `william.mcclean@swiftspend.finance`
* **Adversary Sender Address:** `Accounts.Payable@groupmarketingonline.icu`
* **Suspected Malicious Attachments:** `Direct Credit Advice.html` (approx. 515 bytes) and `Quote.pdf` (107 KB)
* **Delivery Mechanism:** Spear-phishing blast delivered on June 29, 2020, at 06:01 AM, utilizing HTML attachments and a malicious PDF containing embedded redirection URLs.

**Notes:**  
The adversary utilized `groupmarketingonline.icu` to send targeted lures across multiple departments, using business-themed subjects (e.g., "Quote for Services Rendered" and "Direct Credit Advice") to induce compliance. The presence of identical timestamps (06:01) across multiple recipients indicates an automated script or mailer tool was used to execute the campaign simultaneously.

---

### 2. Attachment & URL Redirection Analysis

Both attachment types delivered during the campaign (`Direct Credit Advice.html` and `Quote.pdf`) were extracted and subjected to static code inspection to uncover embedded payloads and redirection paths.

* **HTML File Vector (`Direct Credit Advice.html`):** (See 02_html_file screenshot) 
  Static inspection of the HTML source code revealed a client-side redirect mechanism using JavaScript/Meta Refresh. Opening the file automatically routes the browser to an external landing page parameterized with the recipient's email address.
* **PDF File Vector (`Quote.pdf`):** (See 02_pdf_file screenshot)  
  Parsing the object streams within the PDF file delivered to `william.mcclean@swiftspend.finance` identified an embedded Hyperlink Action (`/URI`) attribute pointing directly to the external phishing kit.
* **Full Target Landing Page (Defanged using CyberChef):**  
  `hxxps[://]kennaroads[.]buzz/data/Update365/office365/40e7baa2f826a57fcf04e5202526f8bd/?email=zoe.duncan@swiftspend[.]finance&error`
* **Redirection Root Domain:** `kennaroads.buzz`
* **Impersonated Service:** `Microsoft` *(Microsoft 365 / Office 365)*

**Notes:**  
Both attachment vectors act as initial delivery mechanisms routing victims to a highly convincing Microsoft 365 credential-harvesting interface hosted on `kennaroads.buzz`. The adversary configured the redirection URLs to dynamically append the victim's email address (`?email=user@swiftspend.finance`). This automatically populates the email field on the spoofed login page, lowering user suspicion and increasing credential submission success.

---

### 3. Open Directory Discovery & Phishing Kit Artifact Analysis (see 03_directory screenshot)

Following the destination URL (`/data/Update365/`), directory traversal was attempted against the root web server path (`/data/`). Due to a server misconfiguration by the adversary, directory listing remained enabled, exposing the backend web server files and hosted assets. (See screenshot 03A)

* **Exposed Directory Path:** `hxxps[://]kennaroads[.]buzz/data/`
* **Retrieved Phishing Kit Archive:** `Update365.zip`
* **Cryptographic Hash (SHA-256):** `ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686`

#### Threat Intelligence & VirusTotal Analysis (see 03_archive_and_VirusTotal_scan screenshot)

The extracted archive hash was cross-referenced against VirusTotal to assess threat classification, historical campaign deployment, and file metadata:

* **Primary Threat Category:** Phishing
* **Secondary Threat Category:** `Trojan`
* **Total Archive File Count:** `42` files
* **First VirusTotal Submission:** `2020-04-08 21:55:50 UTC`
* **Passive DNS / SSL Certificate Creation:** `2020-06-25`

**Notes:**  
The exposure of `/data/` allowed the immediate retrieval of `Update365.zip`, an off-the-shelf Microsoft 365 credential harvesting kit. Cross-referencing the file hash on VirusTotal revealed that this specific archive signature was tagged under the `Trojan` classification alongside generic phishing indicators, containing 42 constituent files (including login templates, PHP processing scripts, and image assets).

### 4. Log Inspection & Exfiltration Code Analysis (see 04_log_file screenshot)

To determine the extent of user compromise and identify the adversary's exfiltration channels, both the hosted log files and the backend processing scripts within `Update365.zip` were analyzed.

#### Captured Log File Inspection
Navigating to `/data/Update365/` revealed exposed log files (`log.txt`) generated by the phishing kit:

* **Confirmed compromised users:** `michael.ascot@swiftspend.finance`, `zoe.duncan@swiftspend.finance`, `derick.marshall@swiftspend.finance` and `michelle.chen@swiftspend.finance` 
* **Observed Victim Behavior:** The users submitted their corporate credentials.

> **Analyst Notes:**  
> The phishing kit logic intentionally presents an "Invalid Password" error message upon the initial submission. This tactic tricks victims into re-entering their password, capturing multiple attempts to ensure accuracy and account for typos.

#### Source Code Analysis (`submit.php`) (see 04_submit.php screenshot)
Static analysis of the primary processing script, `submit.php`, uncovered the data capture and exfiltration routines:

* **Primary Exfiltration Drop Address:** `m3npat@yandex.com`
* **Exfiltration Mechanism:** PHP `mail()` function configured to package captured usernames, passwords, IP addresses, and user-agent strings into automated outbound emails.

**Notes:**  
Once a victim submits credentials, `submit.php` writes the entry to the local server log and immediately dispatches an email payload to the adversary's Yandex inbox (`m3npat@yandex.com`). 

---

## Indicators of Compromise (IOCs)

| IOC Type | Indicator | Description |
| :--- | :--- | :--- |
| **Sender Email** | `Accounts.Payable@groupmarketingonline.icu` | Adversary distribution email address |
| **Phishing Domain** | `kennaroads.buzz` | Infrastructure hosting the harvesting kit |
| **Phishing Kit Archive** | `ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686` | SHA-256 hash of `Update365.zip` |
| **Exfiltration Email** | `m3npat@yandex.com` | Primary adversary collection inbox |

---

## Remediation & Recommendations

1. **Identity & Access Management:** Force an immediate password reset and revoke active SSO/OAuth sessions for all targeted users, prioritizing `michael.ascot@swiftspend.finance` and `zoe.duncan@swiftspend.finance`.
2. **Network Level Blocking:** Block `kennaroads.buzz` and `groupmarketingonline.icu` across DNS resolvers, firewalls, and secure web gateways.
3. **Email Security Controls:** Configure mail gateway rules to quarantine inbound emails originating from `groupmarketingonline.icu` or containing links referencing `/data/Update365/`.
4. **MFA Reinforcement:** Verify Multi-Factor Authentication (MFA) enforcement across all external endpoints to neutralize harvested password replay attempts.
