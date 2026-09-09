<img width="1001" height="498" alt="Alert 2" src="https://github.com/user-attachments/assets/a5128f6e-f65f-435a-adf0-ce4f6bc5bd0e" />

From the alert details we can deduct that **Host:** `WIN-H015` is probably a workstation (servers often use prefixes like `SRV`, `WEB`, `MSQL`) and the **User:** `oliver.thompson` is a System Engineer. Let’s start by filtering by timestamp and querying the **Task Name:** `AssessmentTaskOne` along with **Event ID:** `4698` (which indicates that a scheduled task was created).


<img width="2560" height="1392" alt="A2-1" src="https://github.com/user-attachments/assets/c077621b-5586-4893-a2a7-5811b0a19b18" />


Looking at the Triggers section, we see that the task runs every day at the same time on the workstation, which is suspicious. Let’s inspect the **Exec** and **Principals** sections to see what task is being executed under which user account.


<img width="2560" height="1392" alt="A2-2" src="https://github.com/user-attachments/assets/5c780d65-2f84-4a3a-a6ca-a7e35b3c85a5" />


There are definitely signs of malicious activity: this task uses `certutil` to download `rv.exe` from the `tryhotme` domain into the `Temp` folder under the name of `DataCollector.exe`. It will then launch this file using a `Start-Process` PowerShell command—a clear example of persistence. 

> **Verdict:** True Positive — Escalated to L2 Analyst.

### Recommended Remediation

**1. Isolate the affected workstation**
* Immediately isolate `WIN-H015` from the network to prevent further communication with the attacker or lateral movement.
* Preserve the system for forensic investigation before making significant changes.

**2. Disable the compromised account**
* Investigate the `oliver.thompson` account for signs of compromise.
* Reset the account password and revoke active sessions/tokens if compromise is suspected.
* Review the account's recent authentication activity for unusual logins or access from unexpected systems.

**3. Remove the persistence mechanism**
* Disable and remove the malicious `AssessmentTaskOne` scheduled task.
* Confirm that no additional scheduled tasks were created by the attacker.
* Search for other common Windows persistence mechanisms, including services, Registry Run keys, startup folders, WMI subscriptions, and additional scheduled tasks.

**4. Investigate DataCollector.exe**
* Quarantine and analyse `DataCollector.exe` rather than simply deleting it.
* Determine whether `rv.exe` and `DataCollector.exe` are malicious and identify any associated hashes, URLs, domains, or IP addresses.
* Search the SIEM and endpoint telemetry for other systems that downloaded or executed the same file.

**5. Investigate the download activity**
* Investigate the connection to the `tryhotme` domain.
* Search for other hosts communicating with the domain or downloading the same payload.
* Determine how the attacker initially gained access to `WIN-H015`.

**6. Hunt for additional attacker activity**
* Search for PowerShell execution involving `Start-Process`, `certutil`, and the suspicious executable.
* Review process creation, network connections, authentication events, and file activity around the time the scheduled task was created.
* Check for evidence of privilege escalation or lateral movement.

**7. Eradication and recovery**
* Remove confirmed malicious files and persistence mechanisms.
* If the workstation's integrity cannot be trusted, reimage the system from a known-good source.
* Apply outstanding security patches and verify endpoint security controls are functioning correctly.
* Continue monitoring the workstation and associated user account for further suspicious activity.
