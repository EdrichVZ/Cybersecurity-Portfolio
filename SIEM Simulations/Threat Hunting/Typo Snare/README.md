# Threat Hunting Investigation — Typo Snare

## Overview

This project documents a hands-on threat-hunting investigation performed against the **TryHackMe Threat Hunting Simulator — Typo Snare** scenario.

The objective was to investigate a suspected compromise using Windows security telemetry and Elasticsearch, identify the sequence of malicious activity, correlate events across affected hosts, and reconstruct the attack from initial access through final impact.

Rather than treating individual alerts in isolation, the investigation focused on establishing relationships between:

* Network connections
* Process creation
* PowerShell activity
* Persistence mechanisms
* Credential-access techniques
* Active Directory activity
* Remote access
* Lateral movement
* Data theft
* Ransomware execution

The investigation ultimately revealed a multi-stage intrusion that progressed from an initial malicious download to credential compromise, movement between workstations, privilege abuse, and ransomware deployment.

---

## Investigation Objectives

The primary goals of the investigation were:

1. Identify the initial point of compromise.
2. Determine which host and user were initially affected.
3. Establish how the attacker achieved persistence.
4. Identify credential-access activity.
5. Track movement between compromised systems.
6. Investigate suspicious Active Directory operations.
7. Map observed behaviour to MITRE ATT&CK techniques.
8. Determine the final impact of the intrusion.
9. Construct a chronological attack timeline.

---

## Environment

| Component              | Details                                  |
| ---------------------- | ---------------------------------------- |
| Investigation Platform | TryHackMe Threat Hunting Simulator       |
| Scenario               | Typo Snare                               |
| Log Platform           | Elasticsearch                            |
| Primary Telemetry      | Windows event/process/network logs       |
| Investigation Type     | Threat Hunting                           |
| Focus                  | Endpoint compromise and lateral movement |

---

# Investigation Summary

The investigation identified a staged attack rather than a single malicious event.

The activity can be broadly divided into the following phases:

```text
Initial Access
      ↓
Malicious File Execution
      ↓
PowerShell-Based Payload Execution
      ↓
Persistence
      ↓
Credential Access
      ↓
Internal Discovery
      ↓
Credential Abuse
      ↓
Lateral Movement
      ↓
Active Directory Manipulation
      ↓
Additional Credential Theft
      ↓
Ransomware Deployment
      ↓
Impact
```

---

# 1. Initial Access

The first significant indicator identified during the investigation was an outbound connection from a workstation to infrastructure associated with the malicious activity.

The network telemetry provided the starting point for the investigation.

Rather than immediately assuming that the connection represented compromise, I correlated the network event with subsequent process-creation events to determine what happened immediately afterward.

This correlation was important because the network connection alone did not explain the actual execution chain.

### Investigation approach

I examined:

* Source workstation
* Destination IP
* Destination port
* Timestamp
* Associated user
* Processes created immediately afterward

The timeline showed that the network activity was followed by the retrieval and execution of a suspicious installer.

**Assessment:**
The network connection represents the earliest confirmed indicator in the available telemetry and provides the initial pivot for reconstructing the intrusion.

---

# 2. Malicious Installer Execution

Following the network activity, process telemetry revealed execution of a suspicious MSI installer.

The important observation was not simply that an MSI file executed, but that the execution resulted in additional processes and payload activity.

This established a relationship between the initial network event and the subsequent malicious behaviour.

### Key evidence

```text
Network connection
        ↓
MSI download
        ↓
MSI execution
        ↓
Secondary process execution
```

The installer therefore appears to have acted as the delivery mechanism for the next stage of the intrusion.

---

# 3. PowerShell Payload Execution

The next stage involved PowerShell.

The investigation identified execution patterns consistent with PowerShell retrieving and executing additional code directly in memory.

This is particularly relevant from a defensive perspective because execution from memory can reduce the amount of conventional file-based evidence available to an analyst.

### Hunting considerations

Potential indicators include:

* `powershell.exe`
* `IEX`
* Remote content retrieval
* Encoded or obfuscated commands
* Unusual parent/child process relationships
* PowerShell execution shortly after an external network connection

The combination of these events significantly increased confidence that the original installer was part of a malicious execution chain.

---

# 4. Persistence

The attacker subsequently established persistence through a Windows service.

A service with a name designed to resemble legitimate software activity was created and configured to execute with elevated privileges.

This allowed the attacker to maintain access beyond the original execution event.

### Why this matters

Service-based persistence is particularly valuable to an attacker because it can:

* Survive system reboots
* Execute automatically
* Operate with elevated privileges
* Blend into normal Windows service activity

From a hunting perspective, service creation should therefore be correlated with:

* Service name
* Binary path
* Account used
* Creation timestamp
* Parent process
* Subsequent execution

---

# 5. Credential Access

After establishing persistence, the intrusion shifted toward credential acquisition.

The telemetry showed execution of tooling associated with obtaining credentials from Windows authentication-related memory.

This represented an important escalation in the attack because the objective was no longer simply maintaining access to one workstation.

The attacker was attempting to obtain credentials that could be reused elsewhere in the environment.

---

# 6. Internal Discovery

The attacker then began gathering information about the environment.

Observed discovery activity included commands used to identify local administrative membership and Active Directory information.

This behaviour suggests the attacker was attempting to understand:

* Which accounts had elevated privileges
* Which systems were available
* Which groups were valuable
* Which credentials could enable further access

This phase is particularly important because discovery activity provides opportunities for defenders to detect an attacker **before** lateral movement occurs.

---

# 7. Credential Reuse and Lateral Movement

The investigation subsequently identified activity consistent with credential reuse and lateral movement.

Previously obtained authentication material was used to access another account without requiring the account's plaintext password.

This allowed the attacker to move beyond the original workstation.

### Attack progression

```text
Credential acquisition
        ↓
Credential validation
        ↓
Authentication using stolen material
        ↓
Remote access
        ↓
Execution on another workstation
```

The appearance of remote-management and remote-session processes provided additional evidence that the attacker had successfully moved between hosts.

---

# 8. Active Directory Manipulation

Further investigation revealed attempts to manipulate domain accounts and group membership.

The attacker targeted a privileged Active Directory group and attempted to add a compromised account to that group.

This represented a significant escalation because successful membership modification could provide additional privileges within the domain.

The activity was therefore treated as a high-priority indicator of Active Directory compromise.

---

# 9. Additional Credential Theft

The attacker continued expanding their access by targeting additional accounts.

Active Directory discovery tooling was used to identify relationships within the domain, while credential-dumping activity was subsequently observed against another high-value account.

This demonstrates a common attack pattern:

```text
Compromise workstation
        ↓
Steal credentials
        ↓
Discover AD environment
        ↓
Identify privileged account
        ↓
Obtain additional credentials
        ↓
Move laterally
```

The repeated credential-access activity indicates that the attacker was systematically expanding their control rather than performing opportunistic actions.

---

# 10. Ransomware Deployment

The final stage of the investigation involved execution of a ransomware payload.

The attacker transferred the payload to multiple compromised systems and executed it remotely.

The final telemetry showed the affected hosts ceasing to generate normal investigation events following execution of the ransomware component.

This marked the transition from **intrusion and privilege escalation** to **impact**.

### Final attack progression

```text
Initial compromise
       ↓
Persistence
       ↓
Credential theft
       ↓
Discovery
       ↓
Lateral movement
       ↓
Privilege escalation
       ↓
Additional credential theft
       ↓
Remote execution
       ↓
Ransomware
```

---

# Attack Timeline

| Phase                | Activity                             | Security Significance       |
| -------------------- | ------------------------------------ | --------------------------- |
| Initial Access       | Suspicious external connection       | First known indicator       |
| Execution            | MSI execution                        | Payload delivery            |
| Execution            | PowerShell activity                  | Secondary payload execution |
| Persistence          | Malicious service                    | Survives reboot             |
| Credential Access    | Authentication-related memory access | Credential theft            |
| Discovery            | Local/AD enumeration                 | Environment mapping         |
| Credential Access    | Credential extraction                | Enables account compromise  |
| Lateral Movement     | Remote authentication                | Movement between hosts      |
| Privilege Escalation | AD group manipulation                | Increased privileges        |
| Discovery            | AD relationship enumeration          | Identification of targets   |
| Credential Access    | Additional credential dumping        | Further account compromise  |
| Impact               | Ransomware execution                 | Encryption/disruption       |

---

# MITRE ATT&CK Mapping

The observed behaviour can be mapped to several MITRE ATT&CK tactics and techniques.

| Tactic               | Technique                        | Observed Behaviour                 |
| -------------------- | -------------------------------- | ---------------------------------- |
| Initial Access       | User Execution                   | Malicious file execution           |
| Execution            | PowerShell                       | PowerShell-based payload execution |
| Persistence          | Create or Modify System Process  | Service-based persistence          |
| Credential Access    | OS Credential Dumping            | Authentication material extraction |
| Discovery            | Account Discovery                | Account/group enumeration          |
| Discovery            | Permission Groups Discovery      | Administrative group enumeration   |
| Discovery            | Remote System Discovery          | Identification of additional hosts |
| Credential Access    | Credentials from Password Stores | Browser credential targeting       |
| Lateral Movement     | Remote Services                  | Remote workstation access          |
| Privilege Escalation | Account Manipulation             | Domain/group membership changes    |
| Impact               | Data Encrypted for Impact        | Ransomware execution               |

---

# Detection Opportunities

Several detection opportunities could have exposed the intrusion earlier.

### PowerShell

Alert on suspicious combinations of:

```text
powershell.exe
+
IEX
+
external network connection
```

### Service Creation

Investigate newly created services when:

* The service name resembles legitimate software
* The binary resides in an unusual directory
* The service was created shortly after suspicious PowerShell activity

### Credential Dumping

Monitor for:

* Suspicious access to LSASS
* Unusual process snapshots
* Credential-dumping utilities
* Processes accessing authentication-related memory

### Lateral Movement

Correlate:

```text
New authentication
+
Remote service/session
+
PowerShell execution
```

especially when the source workstation has recently exhibited credential-theft activity.

### Active Directory Changes

High-value detections should monitor:

* Privileged group membership changes
* Unexpected password changes
* Unusual account manipulation
* Administrative activity originating from compromised endpoints

### Ransomware

Potential early-warning indicators include:

* Unusual executable downloads
* Remote execution across multiple workstations
* Rapid file modification
* Suspicious processes appearing simultaneously on multiple hosts

---

# Lessons Learned

This investigation demonstrated the importance of correlating seemingly unrelated events.

A single PowerShell event may not be enough to establish malicious activity. However:

```text
Suspicious network connection
        +
MSI execution
        +
PowerShell
        +
Service creation
        +
Credential access
        +
Lateral movement
```

creates a substantially stronger picture of compromise.

The investigation also reinforced the importance of maintaining a timeline. Individual events became much easier to understand when examined in chronological order and correlated across hosts.

---

# Analyst Takeaways

The main skills demonstrated by this investigation were:

* Windows event-log analysis
* Elasticsearch investigation
* Process-tree analysis
* Network-event correlation
* PowerShell investigation
* Threat hunting
* Attack-chain reconstruction
* Active Directory investigation
* Credential-access detection
* Lateral-movement analysis
* MITRE ATT&CK mapping
* Incident timeline construction

---

## Conclusion

The Typo Snare investigation demonstrated a complete intrusion lifecycle rather than an isolated endpoint compromise.

The attacker progressed from initial execution to persistence, credential theft, discovery, lateral movement, Active Directory manipulation and ultimately ransomware deployment.

The most important lesson from the investigation was the value of **event correlation**.

Rather than investigating alerts independently, linking network activity, process creation, authentication events and Active Directory changes allowed the broader attack chain to be reconstructed.

This is the approach I aim to apply in future SOC and threat-hunting investigations.
