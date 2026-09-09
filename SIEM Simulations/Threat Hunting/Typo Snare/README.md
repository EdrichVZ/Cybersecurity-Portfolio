# Threat Hunting Investigation — Typo Snare

![Threat Hunting](https://img.shields.io/badge/Focus-Threat%20Hunting-blue)
![SIEM](https://img.shields.io/badge/SIEM-Elasticsearch-orange)
![MITRE ATT\&CK](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Platform](https://img.shields.io/badge/Platform-TryHackMe-green)

## Overview

This repository documents a hands-on threat-hunting investigation based on the **Typo Snare** scenario from the TryHackMe Threat Hunting Simulator.

The investigation focuses on reconstructing a ransomware intrusion from endpoint and network telemetry.

Rather than analysing individual alerts in isolation, the investigation uses event correlation to establish relationships between:

* Network connections
* Process creation
* Malicious file execution
* PowerShell activity
* Windows services
* Credential theft
* Active Directory enumeration
* Credential reuse
* Remote access
* Lateral movement
* Privilege escalation
* Browser credential theft
* Ransomware execution

The investigation begins with suspicious network activity associated with a compromised workstation and follows the attacker through multiple stages of the intrusion until the final ransomware deployment.

---

# Investigation Objectives

The main objectives of this investigation were to:

1. Identify the earliest observable indicator of compromise.
2. Determine the initially compromised workstation and user.
3. Establish the execution chain used by the attacker.
4. Identify persistence mechanisms.
5. Investigate credential-access activity.
6. Determine how the attacker moved through the environment.
7. Identify Active Directory abuse.
8. Track the compromise of additional accounts.
9. Reconstruct the complete attack timeline.
10. Map observed behaviour to MITRE ATT&CK.
11. Identify potential detection opportunities.
12. Determine the final impact of the intrusion.

---

# Environment

| Category            | Details                            |
| ------------------- | ---------------------------------- |
| Platform            | TryHackMe Threat Hunting Simulator |
| Scenario            | Typo Snare                         |
| SIEM / Log Platform | Elasticsearch                      |
| Primary Telemetry   | Windows endpoint and network logs  |
| Investigation Type  | Threat Hunting                     |
| Primary Objective   | Reconstruct ransomware intrusion   |
| ATT&CK Framework    | MITRE ATT&CK                       |

---

# Investigation Methodology

The investigation followed a basic threat-hunting workflow:

```text
Initial Indicator
       ↓
Evidence Collection
       ↓
Event Correlation
       ↓
Process Investigation
       ↓
Host & User Identification
       ↓
Attack Chain Reconstruction
       ↓
MITRE ATT&CK Mapping
       ↓
Impact Assessment
```

The key principle throughout the investigation was **correlation**.

An individual event may appear legitimate when viewed independently. Multiple related events occurring on the same host, under the same user, and within a short time period can provide substantially stronger evidence of malicious activity.

---

# Executive Summary

The investigation identified a multi-stage compromise beginning with suspicious external network communication from **WKSTN-03**.

The initial activity was associated with the IP address:

```text
206.189.34.218
```

The affected workstation was:

```text
WKSTN-03
```

and the associated user was:

```text
Perry
```

The attacker subsequently introduced a malicious MSI installer and progressed through PowerShell execution, persistence, credential theft, internal discovery, credential reuse and lateral movement.

The intrusion eventually expanded to **WKSTN-02**, where the attacker performed additional credential theft and privilege-related activity.

Active Directory was subsequently targeted, including manipulation involving the `AD Recovery` group.

The final phase of the intrusion involved deployment of the ransomware executable:

```text
777bomb.exe
```

against multiple compromised systems.

The overall attack can therefore be represented as:

```text
External Infrastructure
        │
        ▼
206.189.34.218
        │
        ▼
WKSTN-03 / Perry
        │
        ▼
Malicious MSI
        │
        ▼
PowerShell
        │
        ▼
Persistence
        │
        ▼
Credential Theft
        │
        ▼
Discovery
        │
        ▼
Credential Reuse
        │
        ▼
Lateral Movement
        │
        ▼
WKSTN-02
        │
        ▼
Active Directory Abuse
        │
        ▼
Additional Credential Theft
        │
        ▼
Remote Execution
        │
        ▼
Ransomware
```

---

# 1. Initial Network Activity

## Indicator

The investigation began with suspicious outbound network traffic involving:

```text
Destination IP: 206.189.34.218
```

The network telemetry identified activity from:

```text
Host: WKSTN-03
User: Perry
```

The earliest activity occurred at approximately:

```text
14:23
```

with additional connections observed later around:

```text
14:25
14:55
```

### Initial Investigation Query

```text
winlog.event_id: 3 AND destination.ip: 206.189.34.218
```

The purpose of this query was to identify all network events associated with the suspicious destination.

### Why This Was Important

The IP address alone does not prove compromise.

However, the network activity became significant when it was correlated with process creation and file-execution events occurring on the same workstation.

This allowed the investigation to move from:

**"A workstation communicated with a suspicious IP."**

to:

**"The workstation subsequently executed files associated with the same attack chain."**

### Screenshot

> Add screenshot of Elasticsearch results showing the suspicious IP and affected host.

`![Initial network activity](screenshots/01-initial-network-activity.png)`

---

# 2. Malicious MSI Execution

Following the initial network activity, process telemetry identified execution of:

```text
7z2301-x64.msi
```

The MSI acted as an initial delivery mechanism for additional malicious activity.

The execution chain was reconstructed as:

```text
WKSTN-03
   ↓
206.189.34.218
   ↓
7z2301-x64.msi
   ↓
MSI542E.tmp
   ↓
PowerShell
```

### Investigation Focus

The important relationship was the timing between:

1. External network communication
2. MSI download
3. MSI execution
4. Child process creation
5. PowerShell execution

This correlation increased confidence that the MSI was part of the intrusion rather than a normal software installation.

### Screenshot

`![Malicious MSI execution](screenshots/02-malicious-msi.png)`

---

# 3. PowerShell Execution

The next stage involved PowerShell.

The process chain showed PowerShell being launched following execution of the MSI-related components.

Of particular interest was the use of:

```text
IEX
```

or `Invoke-Expression`.

`IEX` can be abused to execute dynamically retrieved or constructed PowerShell code.

### Investigation Considerations

When investigating suspicious PowerShell activity, useful fields include:

* Parent process
* Command line
* User
* Host
* Execution timestamp
* Destination IP
* Encoded commands
* Download activity
* Child processes

The PowerShell activity therefore became an important pivot for the investigation.

### Process Relationship

```text
7z2301-x64.msi
        ↓
MSI542E.tmp
        ↓
powershell.exe
        ↓
IEX
```

### Screenshot

`![PowerShell execution](screenshots/03-powershell.png)`

---

# 4. Masquerading

The attacker subsequently introduced an executable named:

```text
7zlegit.exe
```

The filename was designed to resemble legitimate 7-Zip software.

This represents a **masquerading technique**, where malicious software uses a name that could reasonably be mistaken for legitimate software.

The use of a familiar application name can reduce suspicion during casual inspection and may also make manual investigation more difficult.

### Analyst Observation

The filename alone should not be treated as malicious.

The stronger indicator was its relationship with:

```text
7z2301-x64.msi
7zipp.org
7zipp.dll
7zService
```

These artifacts formed a consistent cluster of related activity.

### Screenshot

`![Masquerading executable](screenshots/04-masquerading.png)`

---

# 5. Persistence Through Windows Service

A Windows service named:

```text
7zService
```

was created.

The service operated under:

```text
SYSTEM
```

privileges.

This was a significant finding because service-based persistence can allow malicious code to execute automatically and with elevated privileges.

### Investigation Questions

When investigating a suspicious service, I would examine:

```text
Service Name
Binary Path
Account
Creation Time
Parent Process
Execution Time
```

The service was particularly suspicious because it appeared in the same investigation chain as the previously identified malicious files.

### Attack Chain

```text
7zlegit.exe
      ↓
7zService
      ↓
SYSTEM
      ↓
Persistent execution
```

### Screenshot

`![Malicious service](screenshots/05-service-persistence.png)`

---

# 6. DLL Execution With rundll32

The investigation identified:

```text
7zipp.dll
```

being executed through:

```text
rundll32.exe
```

The use of `rundll32.exe` is important because it is a legitimate Windows executable capable of loading DLL files.

Attackers can abuse legitimate system binaries to execute malicious code while reducing reliance on obviously malicious executables.

### Process Relationship

```text
7zService
     ↓
rundll32.exe
     ↓
7zipp.dll
```

### Analyst Assessment

The combination of:

* Suspicious service
* SYSTEM execution
* `rundll32.exe`
* Unusual DLL
* Earlier malicious MSI activity

provided strong evidence that the service was part of the attack infrastructure.

### Screenshot

`![rundll32 DLL execution](screenshots/06-rundll32.png)`

---

# 7. Credential Access — LSASS

The attacker subsequently shifted toward credential access.

PowerShell activity associated with:

```text
NanoDump.ps1
```

was identified.

The activity targeted:

```text
LSASS.exe
```

The Local Security Authority Subsystem Service is a high-value target because it can contain authentication material associated with logged-in users.

### Additional Indicator

The investigation also identified:

```text
WerFault.exe
```

being used in conjunction with a process identifier related to the dumping activity.

The important point was not simply the presence of `WerFault.exe`, but its relationship with the surrounding credential-access activity.

### Suspicious Chain

```text
PowerShell
     ↓
NanoDump.ps1
     ↓
LSASS
     ↓
Credential material
```

### Screenshot

`![LSASS credential access](screenshots/07-lsass.png)`

---

# 8. Additional Credential Extraction

Another PowerShell script was identified:

```text
pwrex.ps1
```

The script was associated with additional credential extraction activity.

At this point, the investigation indicated that the attacker was actively attempting to obtain reusable authentication material rather than simply maintaining access to the original workstation.

This was an important transition in the attack.

```text
Initial Access
      ↓
Persistence
      ↓
Credential Theft
      ↓
Credential Reuse
```

---

# 9. Local and Domain Discovery

The attacker began gathering information about the compromised environment.

One command of interest was:

```text
net localgroup administrators
```

This command enumerates members of the local Administrators group.

The objective was likely to identify accounts with elevated privileges that could be useful for further access.

### Why Discovery Matters

Discovery activity can provide defenders with an opportunity to detect an attacker before significant lateral movement occurs.

Useful hunting pivots include:

* `net.exe`
* `whoami.exe`
* `net group`
* `net user`
* PowerShell AD commands
* Remote-system enumeration
* Unusual account queries

### Screenshot

`![Local administrator discovery](screenshots/08-discovery.png)`

---

# 10. Credential Reuse and Pass-the-Hash

The investigation subsequently identified activity associated with credential reuse.

The compromised account:

```text
james
```

was associated with Pass-the-Hash activity.

Pass-the-Hash allows an attacker to authenticate using an NTLM hash rather than knowing the user's plaintext password.

This is especially dangerous because stolen credentials can potentially be reused across multiple systems.

### Investigation Chain

```text
Credential Dumping
       ↓
NTLM Hash
       ↓
Pass-the-Hash
       ↓
Authentication
       ↓
Remote Access
```

A subsequent:

```text
whoami
```

command helped establish the security context under which the attacker was operating.

### Screenshot

`![Pass the Hash activity](screenshots/09-pass-the-hash.png)`

---

# 11. Domain Account Manipulation

The attacker subsequently targeted the domain account:

```text
anna.jones
```

Password modification activity was observed, followed by the use of PowerSploit to continue the attack.

The successful operation occurred at approximately:

```text
14:52
```

This activity demonstrated that the attacker had progressed beyond workstation-level compromise and was now interacting with domain resources.

---

# 12. WinRM / Remote PowerShell Activity

Following the domain-account activity, the investigation identified:

```text
wsmprovhost.exe
```

This process is associated with Windows Remote Management and remote PowerShell sessions.

Its appearance provided evidence that the attacker was executing commands remotely.

### Attack Flow

```text
Compromised Credentials
        ↓
Remote Authentication
        ↓
WinRM
        ↓
wsmprovhost.exe
        ↓
Remote Command Execution
```

This was an important indicator of lateral movement.

### Screenshot

`![WinRM activity](screenshots/10-winrm.png)`

---

# 13. Lateral Movement to WKSTN-02

The investigation subsequently identified activity involving:

```text
WKSTN-02
```

The attacker transferred and executed:

```text
7zipp.dll
```

on the secondary workstation.

This demonstrated that the original compromise of `WKSTN-03` had expanded into another endpoint.

### Lateral Movement Model

```text
WKSTN-03
    │
    │ stolen credentials
    ▼
Remote authentication
    │
    ▼
WKSTN-02
    │
    ▼
7zipp.dll
```

This was a significant escalation because the attacker now had access to multiple systems.

### Screenshot

`![Lateral movement](screenshots/11-lateral-movement.png)`

---

# 14. RDP Session Activity

The investigation also identified processes associated with a Remote Desktop session.

Relevant processes included:

```text
TSTheme.exe
winlogon.exe
LogonUI.exe
rdpclip.exe
AtBroker.exe
```

The presence of this group of processes provided evidence consistent with an RDP session.

This added another remote-access mechanism to the attack chain.

### Analyst Note

A single RDP-related process would not necessarily prove malicious activity.

However, when the processes occur alongside:

* Stolen credentials
* Previous lateral movement
* Malicious payloads
* Privilege escalation

the overall context becomes significantly more suspicious.

### Screenshot

`![RDP activity](screenshots/12-rdp.png)`

---

# 15. Sticky Keys Abuse

The attacker subsequently manipulated the Windows Sticky Keys accessibility mechanism involving:

```text
sethc.exe
```

The technique can be abused to obtain command execution from the Windows logon interface.

This provided another avenue for privileged execution on the compromised workstation.

### Security Significance

Accessibility-feature abuse is particularly concerning because the targeted executable can be invoked from the Windows logon environment.

This can potentially provide an attacker with a privileged command shell before a normal interactive desktop session is established.

### Screenshot

`![Sticky Keys abuse](screenshots/13-sticky-keys.png)`

---

# 16. Browser Credential Theft

The attacker then introduced:

```text
Invoke-SharpChromium.ps1
```

The script was associated with targeting credentials and sensitive information stored by Chromium-based browsers.

Browser credential theft can potentially expose access to:

* Internal web applications
* Administrative portals
* Cloud services
* Saved credentials
* Session information

This indicated that the attacker was continuing to expand their available credentials and access paths.

### Investigation Significance

The appearance of browser credential theft after previous credential-dumping activity demonstrates a broader credential-acquisition strategy.

The attacker was targeting multiple sources of authentication material rather than relying on a single technique.

### Screenshot

`![Browser credential theft](screenshots/14-browser-credentials.png)`

---

# 17. Active Directory Group Manipulation

The attacker subsequently investigated:

```text
AD Recovery
```

and manipulated group membership involving:

```text
anna.jones
```

The account was added to the:

```text
AD Recovery
```

group.

The operation was associated with elevated credentials:

```text
itadmin
```

This represented a significant escalation because modification of Active Directory group membership can directly affect an account's privileges.

### Attack Progression

```text
Credential Theft
       ↓
Domain Access
       ↓
Account Manipulation
       ↓
Privileged Group Membership
       ↓
Expanded Access
```

### Screenshot

`![Active Directory manipulation](screenshots/15-ad-manipulation.png)`

---

# 18. Targeting damian.hall

The attacker then turned attention toward:

```text
damian.hall
```

Additional Active Directory enumeration was performed to understand account relationships and available privileges.

Further credential-access activity was subsequently identified.

The attacker again used credential-theft techniques to obtain NTLM authentication material.

This enabled another round of credential reuse and remote access.

### Investigation Observation

The repeated cycle of:

```text
Discovery
   ↓
Credential Theft
   ↓
Credential Reuse
   ↓
Remote Access
```

shows that the attacker was systematically expanding their access within the environment.

---

# 19. Final Payload — Ransomware

The final phase of the investigation involved:

```text
777bomb.exe
```

The executable was associated with:

```text
7zipp.org
```

This was significant because the domain had appeared earlier in the attack chain.

The attacker had therefore maintained infrastructure continuity from the earlier stages of the compromise through to the final impact phase.

### Final Execution

The ransomware was deployed against:

```text
WKSTN-03
WKSTN-02
```

Normal telemetry from the affected systems subsequently ceased.

This marked the transition from:

**intrusion**

to:

**impact**.

### Screenshot

`![Ransomware execution](screenshots/16-ransomware.png)`

---

# Attack Timeline

| Approx. Time | Host                | User / Account | Activity                               |
| ------------ | ------------------- | -------------- | -------------------------------------- |
| 14:23        | WKSTN-03            | Perry          | Connection to `206.189.34.218`         |
| 14:25        | WKSTN-03            | Perry          | Additional suspicious network activity |
| —            | WKSTN-03            | Perry          | `7z2301-x64.msi` executed              |
| —            | WKSTN-03            | Perry          | PowerShell / `IEX` activity            |
| —            | WKSTN-03            | —              | `7zlegit.exe` introduced               |
| —            | WKSTN-03            | —              | `7zService` persistence created        |
| —            | WKSTN-03            | SYSTEM         | `rundll32.exe` → `7zipp.dll`           |
| —            | WKSTN-03            | —              | LSASS credential-access activity       |
| —            | WKSTN-03            | —              | `NanoDump.ps1` / `pwrex.ps1`           |
| —            | WKSTN-03            | —              | Local administrator discovery          |
| —            | WKSTN-03            | james          | Pass-the-Hash                          |
| 14:52        | —                   | anna.jones     | Domain account manipulation            |
| —            | —                   | —              | WinRM / `wsmprovhost.exe`              |
| —            | WKSTN-02            | —              | Lateral movement                       |
| —            | WKSTN-02            | —              | RDP activity                           |
| —            | WKSTN-02            | —              | Sticky Keys abuse                      |
| —            | WKSTN-02            | —              | Browser credential theft               |
| —            | —                   | itadmin        | AD group manipulation                  |
| —            | —                   | damian.hall    | Additional credential targeting        |
| —            | WKSTN-02 / WKSTN-03 | —              | `777bomb.exe` ransomware               |

> **Note:** Exact timestamps should be confirmed against the telemetry from the investigation environment before treating this table as a definitive timeline.

---

# Indicators of Compromise

## Network Indicators

| Type   | Indicator        | Context                            |
| ------ | ---------------- | ---------------------------------- |
| IPv4   | `206.189.34.218` | Suspicious external infrastructure |
| Domain | `7zipp.org`      | Payload-related infrastructure     |

## Host Indicators

| Type    | Indicator     |
| ------- | ------------- |
| Host    | `WKSTN-03`    |
| Host    | `WKSTN-02`    |
| User    | `Perry`       |
| Account | `james`       |
| Account | `anna.jones`  |
| Account | `damian.hall` |
| Account | `itadmin`     |

## File Indicators

| File                       | Context                     |
| -------------------------- | --------------------------- |
| `7z2301-x64.msi`           | Initial malicious installer |
| `MSI542E.tmp`              | Intermediate payload        |
| `7zlegit.exe`              | Masquerading executable     |
| `7zipp.dll`                | Malicious DLL               |
| `NanoDump.ps1`             | Credential-access activity  |
| `pwrex.ps1`                | Credential extraction       |
| `Invoke-SharpChromium.ps1` | Browser credential theft    |
| `777bomb.exe`              | Ransomware payload          |

## Persistence Indicator

```text
7zService
```

---

# MITRE ATT&CK Mapping

The observed activity can be mapped to several MITRE ATT&CK tactics and techniques.

| Tactic                             | Technique                       | Evidence                        |
| ---------------------------------- | ------------------------------- | ------------------------------- |
| Initial Access                     | User Execution                  | Malicious installer execution   |
| Execution                          | PowerShell                      | `powershell.exe` / `IEX`        |
| Execution                          | System Binary Proxy Execution   | `rundll32.exe`                  |
| Persistence                        | Create or Modify System Process | `7zService`                     |
| Defense Evasion                    | Masquerading                    | `7zlegit.exe`                   |
| Credential Access                  | OS Credential Dumping           | LSASS / NanoDump                |
| Credential Access                  | Credentials from Web Browsers   | SharpChromium activity          |
| Discovery                          | Account Discovery               | Account enumeration             |
| Discovery                          | Permission Groups Discovery     | Administrator-group enumeration |
| Discovery                          | Domain Trust / AD Discovery     | Active Directory enumeration    |
| Lateral Movement                   | Remote Services                 | WinRM / RDP                     |
| Lateral Movement                   | Pass the Hash                   | NTLM credential reuse           |
| Privilege Escalation               | Account Manipulation            | Domain/group changes            |
| Persistence / Privilege Escalation | Accessibility Features          | Sticky Keys abuse               |
| Impact                             | Data Encrypted for Impact       | Ransomware execution            |

---

# Detection Opportunities

## 1. Suspicious PowerShell

Potential detection logic:

```text
powershell.exe
+
IEX
+
external network connection
```

Additional context should include:

* Parent process
* User
* Destination
* Command line
* Child processes

---

## 2. Suspicious Service Creation

Monitor newly created services where:

* The service name resembles legitimate software.
* The executable path is unusual.
* The service runs as `SYSTEM`.
* Creation follows suspicious PowerShell activity.
* The binary is unsigned or unfamiliar.

Example indicator:

```text
7zService
```

---

## 3. LSASS Access

Monitor for unusual processes accessing:

```text
LSASS.exe
```

Particularly suspicious combinations include:

```text
PowerShell
+
Credential dumping script
+
LSASS access
```

---

## 4. Pass-the-Hash

Monitor for unusual authentication patterns involving:

* NTLM
* Remote authentication
* Administrative accounts
* New source workstations
* Authentication immediately following credential-dumping activity

---

## 5. WinRM Lateral Movement

A useful hunting pattern is:

```text
New authentication
       +
wsmprovhost.exe
       +
PowerShell
```

especially when the source machine has already exhibited suspicious behaviour.

---

## 6. RDP From Compromised Hosts

Investigate RDP sessions where:

* The source workstation is already compromised.
* Credentials were recently dumped.
* The destination is a sensitive workstation.
* RDP activity occurs shortly after credential theft.

---

## 7. Active Directory Group Changes

Privileged group membership changes should receive high priority.

Particularly suspicious situations include:

```text
Unexpected account
        ↓
Added to privileged group
        ↓
From unusual source
```

---

## 8. Ransomware Behaviour

Potential early indicators include:

* Rapid file modification
* Suspicious executable deployment
* Remote execution
* Multiple hosts receiving the same payload
* Unusual process creation across several endpoints
* Sudden loss of endpoint telemetry

---

# Investigation Lessons

## Event Correlation Is Critical

A single event often provides insufficient context.

For example:

```text
powershell.exe
```

is not automatically malicious.

However:

```text
Suspicious IP
      +
Malicious MSI
      +
PowerShell
      +
IEX
      +
Service Creation
      +
LSASS Access
```

creates a substantially stronger indication of compromise.

---

## Attackers Reuse Credentials

Credential theft was a major component of the intrusion.

The attacker repeatedly followed the pattern:

```text
Steal
 ↓
Validate
 ↓
Reuse
 ↓
Move
 ↓
Steal Again
```

This demonstrates why credential-access events should be correlated with authentication and lateral-movement telemetry.

---

## Lateral Movement Changes the Scope

Once activity moved from:

```text
WKSTN-03
```

to:

```text
WKSTN-02
```

the incident was no longer an isolated endpoint compromise.

The investigation scope had to expand to include:

* Additional hosts
* Additional users
* Authentication events
* Active Directory
* Remote services
* Privileged accounts

---

# Attack Chain Summary

```text
┌──────────────────────────────┐
│ External Infrastructure      │
│ 206.189.34.218               │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ WKSTN-03                     │
│ User: Perry                  │
└──────────────┬───────────────┘
               │
               ▼
      7z2301-x64.msi
               │
               ▼
       PowerShell / IEX
               │
               ▼
         7zlegit.exe
               │
               ▼
          7zService
               │
               ▼
          7zipp.dll
               │
               ▼
       Credential Theft
               │
               ▼
        AD Discovery
               │
               ▼
        Pass-the-Hash
               │
               ▼
        Remote Access
               │
               ▼
┌──────────────────────────────┐
│ WKSTN-02                     │
└──────────────┬───────────────┘
               │
               ├── RDP
               ├── Sticky Keys
               ├── Browser Credential Theft
               │
               ▼
       Active Directory
               │
               ▼
      Account Manipulation
               │
               ▼
      Additional Credentials
               │
               ▼
        Remote Execution
               │
               ▼
          777bomb.exe
               │
               ▼
          RANSOMWARE
```

---

# Analyst Conclusion

The Typo Snare investigation demonstrated a complete multi-stage intrusion rather than a single malicious event.

The attacker initially established access through a malicious software delivery mechanism and subsequently developed persistence on the compromised workstation.

Credential-access techniques were then used to obtain reusable authentication material. This enabled discovery and lateral movement into additional systems.

The attacker subsequently targeted Active Directory and privileged accounts, expanding their control over the environment.

The intrusion ultimately culminated in ransomware execution against multiple workstations.

The investigation highlights several important SOC analyst principles:

* Investigate events in context.
* Build timelines rather than analysing isolated alerts.
* Correlate network and endpoint telemetry.
* Track users as well as hosts.
* Treat credential theft as a potential precursor to lateral movement.
* Monitor privileged Active Directory changes.
* Use MITRE ATT&CK to describe attacker behaviour.
* Identify detection opportunities before the final impact stage.

The most important finding was that the ransomware event was **not the beginning of the incident**.

It was the final stage of an attack that had already involved:

```text
Initial Access
       ↓
Execution
       ↓
Persistence
       ↓
Credential Access
       ↓
Discovery
       ↓
Lateral Movement
       ↓
Privilege Escalation
       ↓
Active Directory Abuse
       ↓
Impact
```

This investigation demonstrates the value of threat hunting in identifying the activity that occurs **before** the final security alert.

---

# Skills Demonstrated

### Security Operations

* Alert investigation
* Threat hunting
* IOC identification
* Incident timeline construction
* Evidence correlation
* Attack-chain reconstruction

### Windows Security

* Windows process analysis
* PowerShell investigation
* Windows services
* LSASS investigation
* RDP analysis
* WinRM investigation
* Windows authentication

### Active Directory

* Account enumeration
* Privileged-group investigation
* Account manipulation
* Credential abuse
* Lateral movement

### SIEM / Log Analysis

* Elasticsearch
* Event filtering
* Process correlation
* Network telemetry analysis
* Host-based investigation

### Threat Intelligence

* IP investigation
* Domain investigation
* IOC collection
* Malware artifact identification

### Frameworks

* MITRE ATT&CK
* Threat-hunting methodology
* Incident-response lifecycle

---

# Tools & Technologies

```text
Elasticsearch
Windows Event Logs
Sysmon
PowerShell
Active Directory
WinRM
RDP
MITRE ATT&CK
TryHackMe Threat Hunting Simulator
```

---

# Disclaimer

This repository documents analysis performed in a controlled **TryHackMe training environment**.

The IP addresses, domains, hostnames, usernames, filenames and other indicators documented here belong to the simulated investigation environment.

The project is intended for:

* Cybersecurity learning
* Threat-hunting practice
* SOC analyst portfolio development
* Detection engineering practice
* MITRE ATT&CK mapping

It should not be interpreted as an investigation of real-world systems.

---

# References

* TryHackMe — Threat Hunting Simulator
* MITRE ATT&CK Framework
* Windows Security / Sysmon documentation

---

## Repository Structure

```text
typo-snare-threat-hunt/
│
├── README.md
│
├── screenshots/
│   ├── 01-initial-network-activity.png
│   ├── 02-malicious-msi.png
│   ├── 03-powershell.png
│   ├── 04-masquerading.png
│   ├── 05-service-persistence.png
│   ├── 06-rundll32.png
│   ├── 07-lsass.png
│   ├── 08-discovery.png
│   ├── 09-pass-the-hash.png
│   ├── 10-winrm.png
│   ├── 11-lateral-movement.png
│   ├── 12-rdp.png
│   ├── 13-sticky-keys.png
│   ├── 14-browser-credentials.png
│   ├── 15-ad-manipulation.png
│   └── 16-ransomware.png
│
└── docs/
    ├── attack-timeline.md
    └── mitre-mapping.md
```

This repository represents a practical threat-hunting exercise focused on reconstructing a multi-stage Windows intrusion and identifying the techniques used throughout the attack lifecycle.
