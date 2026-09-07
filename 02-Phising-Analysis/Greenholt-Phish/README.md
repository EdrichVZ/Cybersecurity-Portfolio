# Incident Analysis Report: Phishing Investigation & Artifact Extraction

**Incident ID:** INC-2026-0907  
**Severity:** High  
**Status:** Closed (Malicious Confirmed)  
**Target:** Greenholt PLC — Sales Department  

---

## Executive Summary

On September 7, 2026, the Security Operations Center (SOC) received an escalation from a sales executive at Greenholt PLC regarding a suspicious email claiming to originate from a known customer. Initial triage revealed multiple red flags, including an uncharacteristic generic greeting, an unsolicited wire transfer request, and an embedded file attachment.

Subsequent technical analysis confirmed that the email was part of a targeted spear-phishing campaign intended to deliver malicious payloads. Email authentication checks indicated domain spoofing, and file analysis revealed a compressed executable hidden under a deceptive PDF extension. The email has been flagged as **Malicious**, and all associated Indicators of Compromise (IOCs) have been extracted for blocklist deployment.

---

## Technical Investigation & Artifact Extraction

### 1. Email Header & Envelope Analysis (see 01_Email screenshot)

An examination of the raw email headers and envelope parameters was conducted to identify the true origin and reply routing of the message.

* **Transfer Reference Number (Subject Line):** `09674321`
* **Header Sender (Display Name):** `Mr. James Jackson`
* **From Address:** `info@mutawamarine.com`
* **Reply-To Address:** `info.mutawamarine@mail.com`

**Notes:**  
The mismatch between the `From` address (`info@mutawamarine.com`) and the `Reply-To` address (`info.mutawamarine@mail.com`) is a tactic designed to divert victim responses to an adversary-controlled public mail domain (`mail.com`), bypassing corporate mail controls.

---

### 2. Network Intelligence & Origin Tracing (see 02_Source_Info screenshot)

To determine the infrastructure used to send the message, the hops recorded in the `Received:` headers were analyzed alongside real-time WHOIS network records.

* **Originating IP Address:** `192.119.71.157`
* **IP Block Owner / Organization:** `HostPapa` (`OrgName: HostPapa`, `ASN: AS54290`)
* **Reverse DNS / PTR Hostname:** `client-192-119-71-157.hostwindsdns.com`

**Notes:**  
Live ARIN WHOIS queries confirm that the allocation `192.119.64.0/18` is directly owned and managed by **HostPapa** (`HOSTP-7`). Reverse DNS resolution maps the node back to legacy `hostwindsdns.com` infrastructure. This verifies that the traffic originated from a commercial VPS hosting provider rather than legitimate corporate mail servers.

---

### 3. Domain Authentication Verification (see 03_SPF_&_DMARC screenshot)

DNS record lookups were performed in the terminal against the `Return-Path` domain (mutawamarine.com) to evaluate email spoofing protections (SPF and DMARC).

* **SPF Record:** `v=spf1 include:spf.protection.outlook.com -all`
* **DMARC Record:** `v=DMARC1; p=quarantine; fo=1`

**Notes:**  
The legitimate domain enforces Microsoft 365 for outbound mail (`spf.protection.outlook.com`) and specifies a `-all` (hard fail) directive. Because the originating IP (`192.119.71.157`) is not an authorized sender in this SPF record, the message fails SPF authentication.

---
### 4. Malicious Attachment & Payload Analysis (see 05_SHA256&VirusTotal screenshot)

The email contained a suspicious attachment designed to mimic a legitimate document. File hash was obtained running SHA256sum command in temrinal.

* **Attachment File Name:** `SWT_#09674321____PDF__.CAB`
* **Cryptographic Hash (SHA-256):** `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`
* **File Size:** `400.26 KB`
* **Actual File Type:** `RAR Archive`

**Notes:**  
While named with `.CAB` and containing `PDF` in the filename to deceive end users, static magic-byte inspection and VirusTotal analysis confirm the file is actually a **RAR compressed archive** containing executable malware payload dropper files.
---
