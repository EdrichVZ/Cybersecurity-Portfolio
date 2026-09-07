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

---
