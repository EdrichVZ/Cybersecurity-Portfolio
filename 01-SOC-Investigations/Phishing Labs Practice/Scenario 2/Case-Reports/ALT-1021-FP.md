# Alert 1021 Case Report - Legitimate System Process (taskhostw Key Roaming)

## 1. Summary
* **Classification:** False Positive
* **Severity:** Low
* **Escalation Required:** No
* **Timestamp:** `09/07/2026 18:07:05.240`
* **Summary:** An alert was generated for process execution on host `win-3451` under the account of Miguel O'Donnell. Investigation confirmed the event was legitimate Windows Scheduled Task execution associated with key roaming and CNG Key Isolation services (`taskhostw.exe`).

---

## 2. Affected Entities & Host Details
* **Hostname:** `win-3451`
* **User Account:** Miguel O'Donnell

---

## 3. Indicators & Process Artifacts
* **Process Name:** `taskhostw.exe` (Host Process for Windows Tasks)
* **Parent Process:** `svchost.exe`
* **File Path:** `C:\Windows\System32\taskhostw.exe`
* **Command Line / Arguments:** `KEYROAMING`
* **Malicious Indicators:** None (Standard Windows system path, expected parent process, and legitimate system argument)

---

## 4. Triage & Analysis

**False Positive Justification:**
`taskhostw.exe` (Host Process for Windows Tasks) was launched by `svchost.exe` with the standard argument `KEYROAMING` from its legitimate binary path (`C:\Windows\System32\`). This represents built-in Windows Scheduled Task activity responsible for CNG Key Isolation / key roaming management rather than malicious behavior or process manipulation.

**Escalation Justification:**
Escalation is not required. The execution lineage, binary path, and arguments reflect benign operating system processes, posing no operational or security risk to the environment.

---

## 5. Resolution & Actions
1. **Case Closure:** Close the alert within the SIEM as a benign False Positive.
2. **Rule Tuning:** Adjust detection logic to filter out benign executions of `taskhostw.exe` when initiated by `svchost.exe` with the `KEYROAMING` parameter to minimize redundant alert noise.

## 6. Screenshot
<img width="2560" height="1392" alt="ALT-1021" src="https://github.com/user-attachments/assets/b7a96983-8343-4bee-b93a-3d6bd8e1a2be" />
