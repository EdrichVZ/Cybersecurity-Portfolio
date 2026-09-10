# Alert 1022 Case Report - Unauthorized Network Share Mapping (net.exe)

## 1. Summary
* **Classification:** True Positive
* **Severity:** High
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:07:49.240`
* **Summary:** A True Positive incident was identified on host `win-3450` (belonging to Michael Ascot, CEO) where `net.exe` was spawned by `powershell.exe` from a local Downloads directory to map a sensitive financial file share (`\\FILESRV-01\SSF-FinancialRecords`). This activity indicates unauthorized network share discovery and potential data staging.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot, CEO)
* **Target Asset:** `\\FILESRV-01\SSF-FinancialRecords`

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `net.exe` (PID: 5784)
* **Working Directory:** `C:\Users\michael.ascot\downloads\`
* **Command Line:** `"C:\Windows\system32\net.exe" use Z: \\FILESRV-01\SSF-FinancialRecords`
* **MITRE ATT&CK Mapping:** T1135 (Network Share Discovery), T1021.002 (Remote Services: SMB/Windows Admin Shares), T1059.001 (Command and Scripting Interpreter: PowerShell)
* **Malicious Indicators:** Execution of `net.exe` via PowerShell originating from an untrusted user directory to connect to restricted financial file shares.

---

## 4. Triage & Analysis

**True Positive Justification:**
Endpoint logs confirm `net.exe` was spawned directly by `powershell.exe` originating from the local user's Downloads directory (`C:\Users\michael.ascot\downloads\`). Legitimate network drive mappings are managed automatically via enterprise Group Policy scripts rather than manual command-line execution from untrusted download locations.

**Escalation Justification:**
The command line explicitly attempts to map network drive `Z:` to a sensitive file share containing restricted financial records (`\\FILESRV-01\SSF-FinancialRecords`). This aligns with adversary tactics for network discovery and lateral movement prior to exfiltration. Given the high privilege level of the affected user account (CEO) and the sensitivity of the targeted data, immediate host containment and incident escalation are required.

---

## 5. Resolution & Actions
1. **Immediate Containment:** Isolate host machine `win-3450` from the network to prevent further lateral movement or data exfiltration. Temporarily disable the Active Directory account for `michael.ascot` or revoke access permissions to `FILESRV-01` until investigation completion.
2. **Investigation & Forensics:** Review file server audit logs on `FILESRV-01` to determine if the drive mapping succeeded and if files were accessed, modified, or copied. Inspect the parent process (`powershell.exe`, PID: 3728) and investigate artifacts in `C:\Users\michael.ascot\downloads\` to identify the initial access vector.
3. **Eradication & Remediation:** Eradicate the malicious script/binary from `C:\Users\michael.ascot\downloads\`. Force an immediate password reset and revoke all active session tokens for user `michael.ascot` post-containment.

## 6. Screenshots
<img width="2556" height="1391" alt="ALT-1022-Log" src="https://github.com/user-attachments/assets/40550c17-9c97-4870-9e3a-dfce4eb8fe53" />
<img width="2560" height="1392" alt="ALT-1022" src="https://github.com/user-attachments/assets/4da0804a-eead-4f3d-abef-f024024ccfbf" />

