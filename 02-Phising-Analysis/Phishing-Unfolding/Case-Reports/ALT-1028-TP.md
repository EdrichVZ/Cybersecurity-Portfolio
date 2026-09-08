# Alert 1028 Case Report - Active Data Exfiltration via DNS Tunneling (nslookup.exe Tertiary Payload Chunk)

## 1. Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:09:34.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) involving an ongoing DNS exfiltration stream. The compromised PowerShell process (PID: 3728) spawned `nslookup.exe` (PID: 3800) from the staged exfiltration directory, transmitting encoded data payload chunks (`nLz8nM0y7NrU0sqtSryCmu40typrsk...`) to the C2 domain `haz4rdw4re.io`.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot)
* **Target C2 Domain:** `nLz8nM0y7NrU0sqtSryCmu40typrsk.haz4rdw4re.io`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `nslookup.exe` (PID: 3800)
* **Working Directory:** `C:\Users\michael.ascot\downloads\exfiltration\`
* **Command Line:** `"C:\Windows\system32\nslookup.exe" nLz8nM0y7NrU0sqtSryCmu40typrsk.haz4rdw4re.io`
* **Event Code:** Sysmon Event ID 1 (Process Create)
* **Threat Activity:** Covert Data Exfiltration & C2 Tunneling (MITRE ATT&CK T1071.004, T1048.003, T1132.001)
* **Malicious Indicators:** Execution of native administrative utility `nslookup.exe` with base64/high-entropy subdomain payloads targeting an external domain (`haz4rdw4re.io`).

---

## 4. Triage & Analysis

**True Positive Justification:**
Sysmon Event ID 1 (Process Creation) logs confirm `powershell.exe` (PID: 3728) spawned `nslookup.exe` (PID: 3800) executing directly out of the local staging folder (`C:\Users\michael.ascot\downloads\exfiltration\`). The command line contains a distinct base64/encoded domain sublabel (`nLz8nM0y7NrU0sqtSryCmu40typrsk`), confirming an active, automated DNS tunneling sequence designed to exfiltrate packaged corporate data under the guise of routine DNS queries.

**Escalation Justification:**
Critical escalation is required. This activity confirms ongoing, automated data exfiltration via DNS queries following the file staging phase (ALT-28). DNS tunneling intentionally circumvents conventional web filtering and proxy controls by leveraging UDP port 53 traffic, signaling active sensitive data leakage from host `win-3450` that requires immediate network containment.

---

## 5. Resolution & Actions
1. **Immediate Host Isolation:** Isolate host `win-3450` from the network immediately to disrupt active DNS exfiltration channels.
2. **Process Termination:** Terminate parent process `powershell.exe` (PID: 3728) and child process `nslookup.exe` (PID: 3800).
3. **DNS Infrastructure Blocking:** Place `haz4rdw4re.io` and all associated wildcard subdomains (`*.haz4rdw4re.io`) on global firewall sinkholes and enterprise DNS blocklists.
4. **Credential Remediation:** Revoke active user sessions and force an immediate domain password reset for account `michael.ascot`.
5. **Exfiltration Impact Assessment:** Analyze DNS resolver query logs for all requests targeting `*.haz4rdw4re.io` and `*.h4z4rdw4re.io` to measure total payload volume and exfiltrated data size.

## 6. Screenshots
<img width="2559" height="1387" alt="ALT-1028-Log" src="https://github.com/user-attachments/assets/893e9f4d-d1ea-407b-bd1d-8d2bdfda29a7" />
<img width="2560" height="1392" alt="ALT-1028" src="https://github.com/user-attachments/assets/22d7b033-250d-4404-a337-f9cfac200f07" />

