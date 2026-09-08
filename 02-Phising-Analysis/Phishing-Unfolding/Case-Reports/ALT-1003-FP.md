# Alert 1003 Case Report - Legitimate System Process (taskhostw Key Roaming)

## 1. Summary
* **Classification:** False Positive
* **Severity:** Low
* **Escalation Required:** No
* **Timestamp:** `09/07/2026 17:39:04.240`
* **Summary:** An alert was generated for process execution on host `win-3451` under the account of Miguel O'Donnell. Investigation confirmed the event was legitimate Windows Scheduled Task execution associated with key roaming (`taskhostw.exe`).

---

## 2. Affected Entities & Host Details
* **Hostname:** `win-3451`
* **User Account:** Miguel O'Donnell

---

## 3. Indicators & Process Artifacts
* **Process Name:** `taskhostw.exe`
* **Parent Process:** `svchost.exe`
* **File Path:** `C:\Windows\System32\taskhostw.exe`
* **Command Line / Arguments:** `KEYROAMING`
* **Malicious Indicators:** None (Standard Windows system path, expected parent process, and legitimate system argument)

---

## 4. Triage & Analysis

**False Positive Justification:**
`taskhostw.exe` was spawned by `svchost.exe` from `C:\Windows\System32\` with the standard system argument `KEYROAMING`. This behavior represents expected, built-in Windows Scheduled Task functionality for user key roaming rather than malicious activity or process injection.

**Escalation Justification:**
Escalation is not required. The execution lineage, binary path, and arguments reflect benign operating system processes, posing no operational or security risk.

---

## 5. Resolution & Actions
1. **Case Closure:** Close the alert within the SIEM as a benign False Positive.
2. **Rule Tuning:** Adjust detection logic to filter out benign executions of `taskhostw.exe` when initiated by `svchost.exe` with the `KEYROAMING` parameter to minimize future alert noise.

## 6. Screenshot
<img width="2560" height="1392" alt="ALT-1003" src="https://github.com/user-attachments/assets/8d089b47-ad46-4607-90fb-cdf01c74258f" />
