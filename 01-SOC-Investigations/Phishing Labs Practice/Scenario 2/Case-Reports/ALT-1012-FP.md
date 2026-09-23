# Alert 1012 Case Report - Legitimate System Process (svchost wsappx Service)

## 1. Summary
* **Classification:** False Positive
* **Severity:** Low
* **Escalation Required:** No
* **Timestamp:** `09/07/2026 17:57:55.240`
* **Summary:** An alert was generated for process activity on host `win-3459` associated with Michelle Smith. Investigation confirmed the execution was legitimate Service Host (`svchost.exe`) activity managing the Windows Store / AppX Deployment Service (`wsappx`).

---

## 2. Affected Entities & Host Details
* **Hostname:** `win-3459`
* **User Account:** Michelle Smith

---

## 3. Indicators & Process Artifacts
* **Process Name:** `svchost.exe` (Service Host)
* **Parent Process:** `services.exe` (Service Control Manager)
* **File Path:** `C:\Windows\System32\svchost.exe`
* **Command Line / Arguments:** `-k wsappx -p`
* **Malicious Indicators:** None (Legitimate system binary path, expected parent process, standard AppX service parameters)

---

## 4. Triage & Analysis

**False Positive Justification:**
`svchost.exe` (Service Host) was launched directly by `services.exe` (Service Control Manager) with the standard argument `-k wsappx -p` from `C:\Windows\System32\`. This represents expected, legitimate Windows Store / AppX Deployment Service management rather than malicious process activity, DLL injection, or persistence execution.

**Escalation Justification:**
Escalation is not required. The execution lineage, binary path, and parameters align completely with normal Windows OS service operations, posing no operational or security risk to the host.

---

## 5. Resolution & Actions
1. **Case Closure:** Close the alert within the SIEM as a benign False Positive.
2. **Rule Tuning:** Update detection logic to whitelist `svchost.exe` when spawned by `services.exe` with standard service group flags (such as `-k wsappx -p`) to reduce unnecessary noise from routine AppX maintenance.

## 6. Screenshot
<img width="2560" height="1392" alt="ALT-1012" src="https://github.com/user-attachments/assets/bbd1e39a-ab06-4540-b921-f00c211620ff" />
