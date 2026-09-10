# SOC Incident Investigation — Compromised Windows Host

## Summary
This investigation simulates a SOC Level 1 response to a potentially compromised Windows endpoint within an organization's HR department.
An IDS alert identified suspicious process execution on an HR workstation. The investigation was conducted using Splunk and Windows Event ID 4688 (Process Creation) logs indexed under `win_eventlogs`.

The investigation identified multiple indicators of compromise, including:
* An impersonation account (`Amel1a`) designed to resemble a legitimate user.
* Suspicious scheduled-task activity associated with an HR user.
* Execution of the Windows LOLBin `certutil.exe`.
* Use of `certutil.exe` to retrieve a payload from an external file-sharing service.
* A connection to `controlc.com`.
* Download and execution of a suspicious executable named `benign.exe`.

The evidence indicates that the affected HR host was compromised and that the attacker used legitimate Windows functionality to download and execute a payload.

---

## Investigation Scope
* **SIEM:** Splunk
* **Log Source:** Windows Event Logs
* **Event ID:** 4688 — Process Creation
* **Index:** `win_eventlogs`
* **Affected Department:** HR
* **Investigation Period:** March 2022

The environment was divided into three departments:

| Department | Users |
| :--- | :--- |
| **IT** | James, Moin, Katrina |
| **HR** | Haroon, Chris, Diana |
| **Marketing** | Bell, Amelia, Deepak |

The known-user list was used as a baseline for identifying anomalous accounts.

---

## Initial Investigation
The investigation began by reviewing the available Windows process-creation events in Splunk. A total of **13,959 events** were identified for March 2022. 

<img width="2560" height="1392" alt="Dashboard" src="https://github.com/user-attachments/assets/7fc85325-a951-419c-a78e-5e2c6802ebd8" />

The investigation then focused on unusual usernames, process execution, scheduled-task activity, command-line arguments, and external connections.

---

## Findings

<img width="2560" height="1392" alt="Log-1" src="https://github.com/user-attachments/assets/ce0855e9-c0b0-4eca-b434-9fbb9fab4d04" />

### Finding 1 — Potential Impersonation Account
A suspicious username, `Amel1a`, was identified in the logs.
* **Legitimate Marketing user:** `Amelia`
* **Observed account:** `Amel1a`

The use of the number `1` in place of the letter `i` is consistent with a potential impersonation or typosquatting technique intended to make a malicious account appear legitimate.

> **Assessment (Severity: Medium):** The account alone does not prove compromise, but it represents a strong anomaly that warrants investigation and correlation with authentication and process-execution logs.

<img width="2560" height="1392" alt="Log-2" src="https://github.com/user-attachments/assets/6526d701-a6e4-45a6-adc3-14ce91446422" />

### Finding 2 — Scheduled Task Activity
Further analysis identified scheduled-task activity associated with `Chris.fort`. Windows Event ID 4698 is associated with the creation of a scheduled task.

Scheduled tasks can be legitimate administrative mechanisms, but attackers frequently abuse them for:
* Persistence
* Privilege escalation
* Automated execution
* Maintaining access after initial compromise

The presence of scheduled-task activity on an HR endpoint therefore increased the confidence that the environment contained malicious activity.

> **Assessment (Severity: High):** Additional investigation should correlate the scheduled task with its creation time, command line, parent process, executable path, and user context.

<img width="2560" height="1392" alt="Log-3" src="https://github.com/user-attachments/assets/cef99fb5-2e79-4e25-b746-3cdd7337fe79" />

### Finding 3 — LOLBin Abuse
The most significant finding involved the HR user `haroon`. Process-creation logs showed the execution of `certutil.exe`.

`certutil.exe` is a legitimate Windows utility associated with certificate services. However, attackers can abuse legitimate Windows binaries to perform malicious actions while potentially avoiding detection mechanisms that focus primarily on unauthorized executables. In this case, `certutil.exe` was used to retrieve a payload from an external resource.

> **Assessment (Severity: Critical):** The combination of suspicious user activity, external resource access, LOLBin execution, and payload retrieval strongly indicates malicious execution rather than normal administrative activity.

### Finding 4 — External Payload Source
The investigation identified `controlc.com` as the third-party service involved in delivering the payload. The infected host connected to:
`https://controlc.com/e4d11035`

The use of an external file-sharing/paste service for payload delivery is suspicious because these services can provide attackers with an easily accessible infrastructure for hosting or retrieving malicious content.

### Finding 5 — Payload Download
The payload retrieved during the post-exploitation phase was saved to the host as `benign.exe`. Despite the filename appearing harmless, the surrounding execution context makes the file suspicious. 

The important SOC principle here is that file names should not be trusted as an indicator of legitimacy. The analyst should instead correlate:
* File creation & process execution
* Parent/child process relationships
* Command-line arguments & user account
* Network connections, hashes, and file reputation

---

## MITRE ATT&CK Mapping

| Technique | MITRE ATT&CK | Evidence |
| :--- | :--- | :--- |
| **Scheduled Task/Job** | T1053 | Scheduled-task activity identified |
| **System Binary Proxy Execution** | T1218 | Abuse of legitimate `certutil.exe` binary |
| **Ingress Tool Transfer** | T1105 | Payload retrieved from external infrastructure |
| **Masquerading** | T1036 | `Amel1a` resembles legitimate `Amelia` account |
| **Command and Scripting Interpreter** | T1059 | Command-line process execution observed |

---

## Indicators of Compromise

| Indicator | Type | Significance |
| :--- | :--- | :--- |
| `Amel1a` | Suspicious Account | Potential impersonation |
| `Chris.fort` | User | Associated with scheduled-task activity |
| `haroon` | User | Associated with LOLBin payload retrieval |
| `certutil.exe` | LOLBin | Used to retrieve payload |
| `controlc.com` | Domain | External payload source |
| `/e4d11035` | URL | Resource accessed by infected host |
| `benign.exe` | File | Suspicious downloaded executable |
| `2022-03-04` | Date | Observed `certutil.exe` execution |

---

## Analyst Assessment
> **Verdict:** TRUE POSITIVE — Suspected Endpoint Compromise

The investigation produced multiple correlated indicators that are unlikely to represent normal user activity. The strongest evidence is the execution of `certutil.exe` by an HR user followed by communication with an external resource and retrieval of a suspicious executable. 

The activity is consistent with an attacker abusing legitimate Windows functionality to download a payload while attempting to blend malicious activity into normal system processes.

---

## Recommended SOC Response

**1. Containment**
* Isolate the affected endpoint from the network.
* Disable or restrict the compromised user account if compromise is confirmed.
* Prevent further communication with identified malicious infrastructure.

**2. Investigation**
* Retrieve the full command line used with `certutil.exe`.
* Identify the parent process and process tree.
* Calculate the hash of `benign.exe` and perform malware analysis and reputation checks.
* Review scheduled-task configuration and investigate authentication activity for affected users.
* Search the SIEM for the same indicators across other endpoints.

**3. Threat Hunting**
* Search for `certutil.exe` execution across the environment.
* Search for connections to `controlc.com` and execution of `benign.exe`.
* Search for the suspicious `Amel1a` account, similar scheduled-task creation events, and other suspicious downloads involving Windows LOLBins.

**4. Recovery**
* Remove malicious persistence mechanisms.
* Reimage the affected workstation if required.
* Reset compromised credentials and restore the endpoint to a trusted state.
* Continue monitoring for related activity.
