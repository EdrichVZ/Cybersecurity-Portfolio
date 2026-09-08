# Alert 1023 Case Report - Data Collection & Staging via Robocopy (exfiltration Folder)

## 1. Summary
* **Classification:** True Positive
* **Severity:** Critical
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:08:36.240`
* **Summary:** A Critical True Positive incident was identified on host `win-3450` (belonging to Michael Ascot) where `powershell.exe` spawned `Robocopy.exe` to recursively copy data from mapped share `Z:\` to a local directory (`C:\Users\michael.ascot\downloads\exfiltration`). This represents active data collection and local staging prior to potential network exfiltration.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot, CEO)
* **Target Network Share:** `Z:\` (Mapped network drive)

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `Robocopy.exe` (PID: 8356)
* **Working Directory:** `Z:\`
* **Command Line:** `"C:\Windows\system32\Robocopy.exe" . C:\Users\michael.ascot\downloads\exfiltration /E`
* **Staging Directory:** `C:\Users\michael.ascot\downloads\exfiltration`
* **Threat Activity:** Data Collection & Staging (MITRE ATT&CK T1039, T1074)
* **Malicious Indicators:** Automated batch file copying from a restricted network share to a designated local staging folder via LOLBin (`Robocopy.exe`).

---

## 4. Triage & Analysis

**True Positive Justification:**
Telemetry confirms `powershell.exe` spawned `Robocopy.exe` from working directory `Z:\` using the `/E` (recursive copy including subdirectories) flag to clone data into `C:\Users\michael.ascot\downloads\exfiltration`. Automated staging of entire directory trees from a network share into a user download directory does not align with legitimate administrative or user behavior and represents malicious data harvesting.

**Escalation Justification:**
Critical escalation is required. The threat actor has progressed past initial discovery and access to active data collection and local staging. `Robocopy.exe` is frequently abused by attackers for rapid, high-volume file transfer. This activity directly precedes network exfiltration, making immediate isolation essential to prevent unauthorized data transfer.

---

## 5. Resolution & Actions
1. **Host Containment:** Isolate host `win-3450` from the network immediately to disrupt active data collection and prevent network exfiltration.
2. **Impact Assessment:** Inspect `C:\Users\michael.ascot\downloads\exfiltration` to quantify and identify all compromised files copied from `Z:\`.
3. **Egress Log Audit:** Review firewall, web proxy, and DNS query logs for host `win-3450` following `18:08:36.240` to confirm whether outbound data exfiltration occurred.
4. **Script Forensics:** Capture and analyze the active PowerShell session/script under PID 3728 to identify scheduled tasks, persistence mechanisms, or secondary execution commands.
5. **Eradication:** Securely delete the local staging folder (`exfiltration`) and terminate all related malicious PowerShell execution trees once forensic collection is finalized.

## 6. Screenshots
<img width="2559" height="1390" alt="ALT-1023-Log" src="https://github.com/user-attachments/assets/be490ef6-df43-44ef-8feb-ee3bfc7c8389" />
<img width="2560" height="1392" alt="ALT-1023" src="https://github.com/user-attachments/assets/217c0b39-66d2-40a9-b1d8-1dbea7c55892" />

