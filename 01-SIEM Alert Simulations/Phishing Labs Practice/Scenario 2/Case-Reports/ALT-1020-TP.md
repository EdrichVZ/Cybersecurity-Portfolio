# Alert 1020 Case Report - Malicious Reconnaissance Tooling (PowerView.ps1)

## 1. Summary
* **Classification:** True Positive
* **Severity:** High
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:05:54.240`
* **Summary:** A True Positive incident was identified on host `win-3450` (belonging to Michael Ascot, CEO) involving the unauthorized drop of `PowerView.ps1` via `powershell.exe` (PID: 9060). PowerView is a known offensive security script used for Active Directory enumeration. Immediate host containment and incident escalation were initiated.

---

## 2. Affected Entities & Host Details
* **Hostname:** `win-3450`
* **User Account:** Michael Ascot (CEO)

---

## 3. Indicators & Process Artifacts
* **Process Name:** `powershell.exe` (PID: 9060)
* **Event Type:** File Creation (`FileCreate`)
* **File Created:** `C:\Users\michael.ascot\Downloads\PowerView.ps1`
* **Threat Category:** Discovery / Domain Reconnaissance (MITRE ATT&CK T1087, T1069)
* **Malicious Indicators:** Unauthorized creation of known offensive PowerShell reconnaissance tooling (`PowerView.ps1`) in a user directory.

---

## 4. Triage & Analysis

**True Positive Justification:**
Telemetry analysis confirmed the creation of `PowerView.ps1` in `C:\Users\michael.ascot\Downloads\ PowerView.ps1` by `powershell.exe` (PID: 9060). PowerView is a well-known offensive security tool utilized by threat actors to perform Active Directory domain enumeration, map domain trusts, and establish network situational awareness. Its unauthorized presence on an endpoint—specifically a executive VIP device—is highly suspicious and indicative of malicious reconnaissance activity.

**Escalation Justification:**
Immediate escalation is required. Threat actors rely on PowerView during the discovery phase to map out Active Directory architecture, identify high-value targets, and plan lateral movement. Given the high-privilege nature of the affected user account (CEO) and the high risk to the broader Active Directory environment, immediate network containment and threat hunting are necessary.

---

## 5. Resolution & Actions
1. **Host Containment:** Quarantine/isolate host machine `win-3450` from the network immediately to halt potential domain enumeration, command-and-control communication, or lateral movement.
2. **Artifact Eradication:** Delete the malicious script located at `C:\Users\michael.ascot\Downloads\PowerView.ps1`.
3. **Account & Log Audit:** Review all recent authentication logs and session activity for `michael.ascot`. Analyze endpoint telemetry and network logs for evidence of script execution, credential dumping, or secondary lateral movement originating from `win-3450`.


## 6. Screenshots
<img width="2559" height="1386" alt="ALT-1020-Log" src="https://github.com/user-attachments/assets/29a27566-e5f2-48e1-afd4-753271bc9f35" />
<img width="2560" height="1392" alt="ALT-1020" src="https://github.com/user-attachments/assets/2b9f85b7-8acb-4dbd-9778-c818e2b09c34" />

