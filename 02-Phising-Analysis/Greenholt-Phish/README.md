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
