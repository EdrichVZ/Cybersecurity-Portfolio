# Alert 1024 Case Report - Defense Evasion & Network Share Unmapping (net.exe /delete)

## 1. Summary
* **Classification:** True Positive
* **Severity:** High
* **Escalation Required:** Yes
* **Timestamp:** `09/07/2026 18:08:47.240`
* **Summary:** A True Positive defense evasion event was identified on host `win-3450` (belonging to Michael Ascot, CEO). The ongoing compromised PowerShell process (PID: 3728) executed `net.exe` to unmap network drive `Z:`. This activity represents automated artifact cleanup following bulk data staging.

---

## 2. Affected Entities & Host Details
* **Source Host:** `win-3450`
* **User Account:** `michael.ascot` (Michael Ascot, CEO)

---

## 3. Indicators & Process Artifacts
* **Parent Process:** `powershell.exe` (PID: 3728)
* **Child Process:** `net.exe` (PID: 8004)
* **Working Directory:** `C:\Users\michael.ascot\downloads\`
* **Command Line:** `"C:\Windows\system32\net.exe" use Z: /delete`
* **Threat Activity:** Defense Evasion / Anti-Forensics (MITRE ATT&CK T1070)
* **Malicious Indicators:** Automated teardown of mapped network drive `Z:` immediately following bulk file collection via identical parent execution context (PID 3728).

---

## 4. Triage & Analysis

**True Positive Justification:**
Telemetry demonstrates `net.exe use Z: /delete` spawned directly by the compromised PowerShell session (PID: 3728). This action immediately followed the unauthorized mapping of `\\FILESRV-01\SSF-FinancialRecords` (ALT-27) and the subsequent bulk file staging via `Robocopy.exe` (ALT-28). The sequence reflects an automated attack script conducting post-collection cleanup to minimize endpoint visibility.

**Escalation Justification:**
High-priority escalation is required. By deleting the mapped share artifact immediately after staging collected files, the threat actor attempts to cover tracks and delay discovery by users or administrators. Completion of this cleanup phase indicates that data harvesting is complete and exfiltration operations are either actively underway or imminent.

---

## 5. Resolution & Actions
1. **Immediate Containment:** Confirm host `win-3450` is fully isolated from the network. Disable the Active Directory account for `michael.ascot` until containment and investigation are finalized.
2. **Timeline & Log Correlation:** Correlate this activity with ALT-27 (Share Mapping) and ALT-28 (Robocopy Staging). Query PowerShell Script Block Logging (Event ID 4104) for PID 3728 to extract the complete script and identify trailing execution commands.
3. **Egress Monitoring:** Monitor network perimeter logs (firewall/proxy/DNS) for `win-3450` surrounding this timestamp to identify potential outbound data transfers originating from `C:\Users\michael.ascot\downloads\exfiltration`.
4. **Remediation:** Keep the host offline until all staged exfiltration artifacts, malicious PowerShell processes, and secondary persistence mechanisms are fully purged.

## 6. Screenshots
<img width="2558" height="1391" alt="ALT-1024-Log" src="https://github.com/user-attachments/assets/0e160b30-0a56-4dad-a9c2-6f7918d3a864" />
<img width="2560" height="1392" alt="ALT-1024" src="https://github.com/user-attachments/assets/7eb9f112-4705-4841-b759-deff47accd6f" />
