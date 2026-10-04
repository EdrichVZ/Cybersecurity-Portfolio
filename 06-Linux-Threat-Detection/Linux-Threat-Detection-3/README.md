# Linux Threat Detection 3 — Post-Exploitation & Persistence Investigation

## Overview

This investigation focuses on detecting and reconstructing **post-exploitation activity on a compromised Linux system**.

Initial access represents only the beginning of an intrusion. Once attackers obtain command execution, they commonly attempt to improve their access, discover sensitive information, escalate privileges and establish persistence.

The investigation follows a compromise from an exposed application through several stages of attacker activity:

- Command injection
- Reverse-shell execution
- Credential discovery
- Privilege escalation
- Systemd service persistence
- Cron persistence
- Privileged account creation
- SSH-key persistence

Linux audit telemetry, authentication logs and system configuration were analysed to determine how the attacker progressed from limited application-level access to persistent privileged access.

The primary objective was to understand how individual post-exploitation events can be correlated into a complete attack chain.

---

# Investigation Objectives

The investigation focused on the following objectives:

- Identify command injection against an internet-facing application
- Determine the account context of executed commands
- Detect reverse-shell activity
- Investigate suspicious networking utilities
- Identify the external system receiving the reverse shell
- Detect credential discovery
- Identify recursive searches for sensitive information
- Investigate access to environment configuration files
- Detect privilege escalation to `root`
- Identify malicious systemd service creation
- Investigate cron-based persistence
- Detect creation of unexpected privileged user accounts
- Identify modifications to SSH `authorized_keys`
- Correlate multiple persistence techniques
- Reconstruct the complete post-exploitation attack chain

---

# Skills Demonstrated

- Linux post-exploitation investigation
- Command injection analysis
- Reverse-shell detection
- `auditd` analysis
- `ausearch`
- Process and command-line investigation
- Credential discovery detection
- Sensitive-file analysis
- Privilege-escalation investigation
- Systemd persistence detection
- Cron persistence detection
- User-account monitoring
- SSH-key persistence detection
- `/var/log/auth.log` analysis
- Parent/child process correlation
- Incident timeline reconstruction
- Attacker TTP correlation

---

# Data Sources & Tools

| Source / Tool | Purpose |
| :--- | :--- |
| `auditd` | Record process execution and file activity |
| `ausearch` | Search and interpret Linux audit events |
| `/var/log/auth.log` | Analyse authentication and user-management activity |
| `grep` | Search files and logs for relevant activity |
| `cat` | Examine configuration and persistence files |
| `crontab` | Review scheduled tasks |
| `systemctl` | Investigate systemd services |
| `/etc/systemd/system/` | Identify persistent system services |
| `/root/.ssh/authorized_keys` | Investigate SSH-key persistence |
| Process IDs / PPIDs | Reconstruct process relationships |

---

# 1. Initial Application Compromise

The investigation begins with an internet-facing application that accepts user-controlled input.

The application performs a network operation based on the supplied value.

A normal input might resemble:

```text
127.0.0.1
```

However, insufficient input validation can allow additional commands to be appended.

For example:

```bash
127.0.0.1 && whoami
```

Instead of performing only the intended operation, the application executes:

```bash
whoami
```

on the underlying operating system.

This behaviour indicates a **command injection vulnerability**.

---

# 2. Determining Execution Context

One of the first questions during application-compromise analysis should be:

> **Which operating-system account is executing the injected commands?**

The command:

```bash
whoami
```

revealed that application-level commands were executing as:

```text
svctrypingme
```

This indicates that the compromised application was running under a restricted service account rather than directly as `root`.

The initial attack context can therefore be represented as:

```text
External Attacker
       ↓
Internet-Facing Application
       ↓
Command Injection
       ↓
svctrypingme
```

Although the attacker does not yet have privileged access, command execution provides a valuable foothold.

---

# 3. From Command Injection to Reverse Shell

Command injection can be restrictive because the attacker may have to submit individual commands through the vulnerable application.

A common next step is therefore to establish a **reverse shell**.

A reverse shell allows the compromised host to initiate a network connection back to an attacker-controlled system.

The attacker then receives an interactive command-line session.

Conceptually:

```text
Attacker System
      ▲
      │
TCP Connection
      │
Compromised Linux Host
      ▲
      │
Shell
      ▲
      │
Vulnerable Application
```

This provides substantially more control than individual command injection.

---

# 4. Detecting Reverse-Shell Utilities

Linux audit telemetry can be searched for utilities frequently associated with reverse shells.

Examples include:

```text
socat
nc
netcat
bash
python
perl
php
```

For example:

```bash
ausearch -i -x socat
```

or:

```bash
ausearch -i -x nc
```

The objective is not simply to determine whether the utility executed.

The analyst should examine:

```text
Executable
Command-line arguments
Parent process
Executing user
Timestamp
Destination address
Destination port
```

These fields provide the context necessary to determine whether the process represents legitimate networking activity or a reverse shell.

---

# 5. Reverse-Shell Process Context

A utility such as `socat` is not automatically malicious.

It has legitimate administrative uses.

Consider:

```text
Administrator
     ↓
Terminal
     ↓
socat
```

This may be expected.

However:

```text
Web Application
      ↓
Shell
      ↓
socat
      ↓
External TCP Connection
```

is substantially more suspicious.

The combination of:

```text
Internet-facing application
+
Command injection
+
Shell creation
+
Networking utility
+
External connection
```

provides strong evidence of reverse-shell activity.

---

# 6. External Reverse-Shell Connection

Audit telemetry identified a reverse-shell connection to:

```text
10.14.105.255
```

The activity can therefore be reconstructed as:

```text
Internet-Facing Application
           ↓
     Command Injection
           ↓
      svctrypingme
           ↓
        socat
           ↓
     10.14.105.255
           ↓
    Interactive Shell
```

At this stage, the attacker has progressed from limited command injection to an interactive remote shell.

---

# 7. Post-Exploitation Discovery

After obtaining an interactive shell, attackers commonly begin searching the compromised system for information that can improve their level of access.

Potential targets include:

```text
Passwords
API keys
Database credentials
SSH keys
Configuration files
Environment variables
Application secrets
Cloud credentials
Administrative scripts
```

The attacker began searching files for references to passwords.

A command used during the investigation was:

```bash
grep -iR pass .
```

Breaking down the command:

```text
grep
  ↓
Search file contents

-i
  ↓
Case-insensitive search

-R
  ↓
Recursive directory search

pass
  ↓
Search term

.
  ↓
Current directory
```

This is a common form of **credential discovery**.

---

# 8. Why Recursive Credential Searches Matter

The `grep` command itself is not malicious.

Developers and administrators frequently use it.

Context is what makes the activity significant.

For example:

```text
Developer
   ↓
Source Code Directory
   ↓
grep "password"
```

may be legitimate.

However:

```text
Compromised Service Account
         ↓
Reverse Shell
         ↓
grep -iR pass .
         ↓
Sensitive Configuration Files
```

strongly suggests that the attacker is searching for credentials.

---

# 9. Sensitive Environment Files

The credential search identified:

```text
.env.local
```

Environment files commonly contain application configuration such as:

```text
Database usernames
Database passwords
API keys
Authentication secrets
Service credentials
Application tokens
```

These files can become valuable targets during post-exploitation.

The attacker accessed the file using:

```bash
cat .env.local
```

This exposed sensitive credentials that could be used for privilege escalation.

---

# 10. Credential Discovery Chain

The activity can be represented as:

```text
Reverse Shell
      ↓
Recursive File Search
      ↓
grep -iR pass .
      ↓
.env.local Identified
      ↓
cat .env.local
      ↓
Credential Exposure
```

This illustrates an important investigation principle:

> **Credential access may begin with ordinary file-search commands rather than specialised credential-stealing malware.**

---

# 11. Privilege Escalation

The attacker initially operated as:

```text
svctrypingme
```

This was a restricted service account.

Credentials discovered inside the environment file enabled the attacker to escalate privileges.

The attacker executed:

```bash
su root
```

The `su` utility allows a user to switch to another account when the required credentials are known.

The privilege-escalation sequence therefore became:

```text
svctrypingme
      ↓
Credential Discovery
      ↓
Sensitive Environment File
      ↓
Privileged Credential Obtained
      ↓
su root
      ↓
Root Access
```

---

# 12. Why Root Access Changes the Incident

Obtaining `root` access significantly increases the severity of a Linux compromise.

A root-level attacker may be able to:

```text
Modify system configuration
Create users
Install software
Disable security controls
Read protected files
Access application secrets
Modify logs
Create scheduled tasks
Install persistent services
Add SSH keys
Modify firewall rules
Access other users' data
```

At this stage, containment becomes critical because the attacker effectively controls the system.

---

# 13. Detecting Privilege Escalation

Commands associated with account switching and privilege escalation include:

```text
su
sudo
sudoedit
pkexec
```

Relevant events should be correlated with:

```text
Previous user
Target user
Timestamp
Authentication result
Parent process
Previous credential-discovery activity
Subsequent privileged actions
```

For example:

```text
Credential Search
      ↓
Sensitive File Access
      ↓
su root
      ↓
Successful Root Session
```

provides much stronger evidence than analysing `su` independently.

---

# 14. Persistence

After obtaining privileged access, an attacker may attempt to ensure continued access even if:

- The original vulnerability is patched
- The current session ends
- The compromised password is changed
- The server reboots

This is known as **persistence**.

Multiple persistence mechanisms were identified during the investigation:

```text
Systemd Service
Cron Job
Privileged User Account
SSH Authorized Key
```

Using several methods improves the attacker's chances of maintaining access if one mechanism is discovered and removed.

---

# 15. Systemd Service Persistence

Linux systems commonly use `systemd` to manage services.

Service definitions are frequently stored inside:

```text
/etc/systemd/system/
```

Attackers with elevated privileges can abuse this functionality by creating a service that automatically executes malicious code.

A suspicious service may appear similar to:

```ini
[Unit]
Description=System Update Service

[Service]
ExecStart=/path/to/malicious/executable

[Install]
WantedBy=multi-user.target
```

If enabled, the malicious executable may run automatically when the system starts.

---

# 16. Detecting Systemd Persistence

Audit telemetry can be searched for activity involving:

```text
/etc/systemd/system/
```

An analyst should investigate:

```text
New service files
Recently modified service files
Unexpected service names
Unknown executable paths
Services created by unusual users
Services created shortly after compromise
```

A suspicious service identified during the investigation was:

```text
tux.service
```

The service definition should then be inspected using:

```bash
cat /etc/systemd/system/tux.service
```

The most important field is:

```text
ExecStart=
```

because it identifies the executable launched by the service.

---

# 17. Systemd Persistence Chain

The activity can be represented as:

```text
Root Access
    ↓
Create Service File
    ↓
/etc/systemd/system/tux.service
    ↓
Configure ExecStart
    ↓
Enable Service
    ↓
Malware Starts Automatically
```

This allows malicious code to survive system reboots.

---

# 18. Why Service Creation Requires Context

Not every newly created systemd service is malicious.

Legitimate software installations frequently create them.

Analysts should examine:

```text
Service name
Creation time
Creator
Executable path
Binary reputation
Parent process
Installation history
Surrounding security events
```

For example:

```text
Package Manager
      ↓
Known Software
      ↓
Expected Service
```

is likely legitimate.

However:

```text
Compromised Root Session
        ↓
Unknown Service
        ↓
Unknown Executable
        ↓
Automatic Startup
```

is substantially more suspicious.

---

# 19. Cron Persistence

Another common Linux persistence technique involves **cron jobs**.

Cron allows commands or scripts to execute automatically according to a schedule.

User cron jobs can be reviewed using:

```bash
crontab -l
```

Attackers may configure entries such as:

```text
@reboot /path/to/malicious/script.sh
```

The special `@reboot` directive causes the configured command to execute whenever the system starts.

---

# 20. Detecting Suspicious Cron Jobs

Analysts should examine scheduled tasks for:

```text
Unknown scripts
Executables inside temporary directories
Commands using curl or wget
Reverse-shell commands
Obfuscated shell commands
Recently created entries
@reboot execution
Unexpected privileged cron jobs
```

A suspicious entry may appear as:

```text
@reboot /var/tmp/.system/update.sh
```

The analyst should then investigate the referenced script.

---

# 21. Cron Persistence Chain

The attack sequence becomes:

```text
Root Access
     ↓
Modify Crontab
     ↓
Add @reboot Job
     ↓
Reference Malicious Script
     ↓
System Reboots
     ↓
Malicious Script Executes
```

Like systemd persistence, cron enables malicious code to survive reboot cycles.

---

# 22. Multiple Startup Persistence Methods

The presence of both:

```text
Systemd Service
+
Cron Job
```

is particularly important.

Attackers may intentionally deploy redundant persistence.

If an administrator removes the malicious systemd service but fails to investigate cron, the attacker may still regain access.

This reinforces the need for **comprehensive persistence hunting** after a privileged compromise.

---

# 23. Account Persistence

Attackers with root privileges can also create additional user accounts.

Linux account-management commands include:

```text
useradd
usermod
adduser
passwd
```

Authentication logs can be searched for these events.

For example:

```bash
grep -E 'useradd|usermod' /var/log/auth.log
```

This can reveal unexpected account creation or privilege changes.

---

# 24. Privileged Account Creation

During the investigation, a new account named:

```text
koichi
```

was created.

The account was subsequently added to the:

```text
sudo
```

group.

The sequence can be represented as:

```text
Root Compromise
      ↓
Create koichi
      ↓
Modify Group Membership
      ↓
Add to sudo
      ↓
Persistent Privileged Account
```

This provides the attacker with another method of obtaining privileged access.

---

# 25. Why New Accounts Matter

Unexpected account creation after compromise should receive immediate attention.

The analyst should determine:

```text
Who created the account?
When was it created?
What groups was it added to?
Does it have a password?
Does it have SSH keys?
Has it authenticated?
What commands has it executed?
```

A newly created account becomes even more suspicious if it belongs to:

```text
sudo
wheel
admin
```

or another privileged group.

---

# 26. Detecting Suspicious Account Changes

Potential indicators include:

```text
Unexpected useradd execution
Unexpected usermod execution
New UID
New home directory
Addition to privileged groups
Password changes
Unexpected shell assignment
Immediate SSH login
```

An account created during an active compromise should generally be treated as hostile until proven otherwise.

---

# 27. SSH Key Persistence

Linux SSH supports public-key authentication.

Authorised public keys are typically stored inside:

```text
~/.ssh/authorized_keys
```

For the root account:

```text
/root/.ssh/authorized_keys
```

Anyone possessing the corresponding private key may be able to authenticate without knowing the account password.

This makes `authorized_keys` an attractive persistence target.

---

# 28. Detecting authorized_keys Modification

Audit telemetry can be used to investigate modifications to sensitive SSH files.

For example:

```bash
ausearch -i -f /root/.ssh/authorized_keys
```

The analyst should determine:

```text
When the file changed
Which user modified it
Which process modified it
Whether a new key was added
Whether the key is authorised
Whether SSH authentication followed
```

The investigation identified modification of:

```text
/root/.ssh/authorized_keys
```

This provided another persistence mechanism.

---

# 29. Why SSH-Key Persistence Is Powerful

Password resets may not remove SSH-key access.

Consider:

```text
Attacker Steals Password
        ↓
Administrator Changes Password
        ↓
Attacker Loses Password Access
```

However, if the attacker previously added a public key:

```text
Attacker Adds SSH Key
        ↓
Administrator Changes Password
        ↓
SSH Key Still Authorised
        ↓
Attacker Can Reconnect
```

Therefore, password rotation alone may be insufficient after a Linux compromise.

---

# 30. Persistence Overview

Four persistence mechanisms were identified.

| Persistence Method | Purpose |
| :--- | :--- |
| **Systemd service** | Automatically launch malicious code |
| **Cron `@reboot` job** | Execute malicious script after reboot |
| **Privileged user account** | Maintain alternate administrative access |
| **SSH authorized key** | Enable passwordless remote access |

These mechanisms provide overlapping paths for continued access.

---

# 31. Reconstructing the Complete Attack Chain

By correlating all available evidence, the incident can be reconstructed as:

```text
External Attacker
       │
       ▼
Internet-Facing Application
       │
       ▼
Command Injection
       │
       ▼
svctrypingme
       │
       ▼
Reverse Shell
       │
       ▼
Credential Discovery
       │
       ├── grep -iR pass .
       │
       └── .env.local
       │
       ▼
Sensitive Credential Obtained
       │
       ▼
su root
       │
       ▼
Privilege Escalation
       │
       ▼
Root Access
       │
       ├───────────────────────────────┐
       │                               │
       ▼                               ▼
Systemd Persistence              Cron Persistence
       │                               │
       │                               │
       ├───────────────┬───────────────┘
       │               │
       ▼               ▼
Account Persistence   SSH Key Persistence
       │               │
       ▼               ▼
koichi + sudo      authorized_keys
       │               │
       └───────┬───────┘
               │
               ▼
       Persistent Access
```

The attacker progressed from limited command execution to multiple independent methods of maintaining privileged access.

---

# 32. Key Findings

| Finding | Assessment |
| :--- | :--- |
| Application allowed operating-system command execution | Critical |
| Commands executed under service account | Initial foothold |
| `socat` used for external connection | Reverse-shell indicator |
| Connection to `10.14.105.255` | External shell destination |
| Recursive password search performed | Credential discovery |
| `.env.local` accessed | Sensitive-file access |
| `su root` executed | Privilege escalation |
| Root access obtained | Critical compromise |
| `tux.service` created | Systemd persistence |
| `@reboot` cron job identified | Cron persistence |
| `koichi` account created | Account persistence |
| `koichi` added to `sudo` | Privileged persistence |
| `/root/.ssh/authorized_keys` modified | SSH-key persistence |

---

# 33. Indicators of Compromise

## Compromised Service Account

```text
svctrypingme
```

---

## Reverse-Shell Destination

```text
10.14.105.255
```

---

## Credential Discovery Command

```bash
grep -iR pass .
```

---

## Sensitive File

```text
.env.local
```

---

## Privilege Escalation

```bash
su root
```

---

## Suspicious Systemd Service

```text
tux.service
```

---

## Suspicious User Account

```text
koichi
```

---

## Privileged Group

```text
sudo
```

---

## SSH Persistence File

```text
/root/.ssh/authorized_keys
```

---

# 34. Analyst Reasoning

The most important aspect of this investigation was recognising that post-exploitation activity should not be investigated as disconnected events.

For example:

```text
grep -iR pass .
```

could simply be a developer searching source code.

Likewise:

```text
su root
```

could be legitimate administration.

Creating a systemd service may also be normal.

Adding an SSH key can be completely legitimate.

However, when the sequence is:

```text
Application Exploitation
       ↓
Reverse Shell
       ↓
Credential Search
       ↓
Sensitive File Access
       ↓
su root
       ↓
Systemd Service
       ↓
Cron Job
       ↓
Privileged Account
       ↓
SSH Key
```

the individual events become part of a clear post-exploitation attack.

---

# 35. Key SOC Lessons

## Initial Access Is Only the Beginning

Detecting the initial compromise is not enough.

Once malicious access has been confirmed, analysts must determine:

```text
What did the attacker execute?
What credentials were exposed?
Did privilege escalation occur?
Was persistence created?
Were additional accounts compromised?
Can the attacker still reconnect?
```

Stopping the investigation after identifying command injection could leave multiple persistence mechanisms undiscovered.

---

## Credential Discovery Often Uses Normal Tools

Attackers do not always require specialised credential-stealing utilities.

Commands such as:

```text
grep
find
cat
less
strings
```

can be sufficient to locate sensitive information.

The analyst must consider execution context rather than relying solely on known malicious binaries.

---

## Sensitive Configuration Files Need Protection

Files such as:

```text
.env
.env.local
config.php
application.yml
settings.json
```

may contain credentials or secrets.

A low-privileged application compromise can therefore become a root compromise if sensitive credentials are stored insecurely.

---

## Privilege Escalation Changes Investigation Priority

Once an attacker reaches `root`, assume they may have modified anything on the host.

This includes:

```text
Accounts
SSH keys
Scheduled tasks
Services
Logs
Security tools
Network configuration
Application files
```

A root compromise requires a significantly broader investigation than a limited user compromise.

---

## Persistence Is Often Redundant

Attackers may establish several persistence mechanisms simultaneously.

Removing one mechanism does not guarantee eradication.

For example:

```text
Remove malicious service
        ↓
Cron persistence remains

Remove cron job
        ↓
Backdoor account remains

Reset password
        ↓
SSH key remains
```

All persistence mechanisms must be identified and removed.

---

## authorized_keys Must Be Checked After Linux Compromise

Resetting passwords is insufficient if unauthorised SSH keys remain configured.

Analysts should review:

```text
/root/.ssh/authorized_keys
/home/*/.ssh/authorized_keys
```

after significant Linux compromises.

---

## Account Creation Is High-Value Evidence

New privileged accounts created during an intrusion provide both evidence of compromise and a potential path back into the environment.

Account creation and group membership changes should be monitored closely.

---

# 36. Recommended SOC Response

If this activity were identified in a production environment, appropriate response actions would include:

1. **Immediately isolate the compromised Linux host** from the network.

2. **Preserve relevant audit, authentication, application and network logs** before remediation.

3. **Terminate confirmed malicious remote sessions**.

4. **Disable compromised and attacker-created accounts**.

5. **Rotate exposed credentials**, including application and privileged credentials.

6. **Remove unauthorised SSH keys** from all affected accounts.

7. **Investigate all systemd services** created or modified during the compromise window.

8. **Review all cron jobs and scheduled tasks** for unauthorised entries.

9. **Remove malicious services, scripts and executables** after preserving required forensic evidence.

10. **Search other systems for the same accounts, SSH keys, files, IP addresses and persistence mechanisms**.

11. **Investigate authentication activity involving the attacker-created account** to determine whether it was used elsewhere.

12. **Review outbound connections** for additional attacker infrastructure.

13. **Patch or remediate the vulnerable application** responsible for initial command execution.

14. **Review application permissions** to reduce access to sensitive files.

15. **Determine whether exposed credentials were reused on other systems**.

16. **Consider rebuilding the affected host** where root-level compromise prevents confidence in the integrity of the operating system.

---

# 37. Defence Improvements

Several defensive improvements could reduce the likelihood or impact of similar attacks.

## Application Security

- Validate and sanitise user input
- Avoid passing untrusted input directly to system shells
- Run applications using least-privileged accounts
- Implement appropriate application security testing

## Credential Security

- Avoid storing privileged credentials in application-accessible files
- Use secrets-management solutions
- Apply strict file permissions
- Rotate sensitive credentials regularly

## Linux Monitoring

Monitor for:

```text
Unexpected shell execution
Changes to /etc/systemd/system/
Changes to crontabs
useradd / usermod activity
Changes to authorized_keys
Unexpected su / sudo activity
Executables running from temporary directories
```

## SSH Hardening

Consider:

```text
Disable direct root SSH login
Prefer key-based authentication
Restrict SSH access by network
Monitor authorized_keys changes
Apply MFA where supported
```

---

# Conclusion

This investigation demonstrated how an attacker can progress from **limited application-level command execution to complete and persistent control of a Linux system**.

The incident began with command injection against an internet-facing application. The attacker used this foothold to establish a reverse shell, providing a more stable interactive session under the application's service account.

The attacker then performed credential discovery using standard Linux utilities and located sensitive information inside an environment configuration file. These credentials were subsequently used to escalate privileges to `root`.

Root access dramatically expanded the attacker's capabilities.

Multiple persistence mechanisms were then established through:

- A malicious systemd service
- A scheduled cron job
- A newly created privileged user account
- An unauthorised SSH public key

The most important lesson from the investigation was that **successful containment requires understanding the complete post-exploitation chain**.

Detecting the original application compromise or reverse shell would not have been enough. Even after terminating the attacker's session and changing the compromised password, the system would remain vulnerable if the malicious service, cron job, privileged account or SSH key remained in place.

The investigation can be summarised as:

```text
Command Injection
       ↓
Reverse Shell
       ↓
Credential Discovery
       ↓
Privilege Escalation
       ↓
Root Compromise
       ↓
Multiple Persistence Mechanisms
       ↓
Persistent Privileged Access
```

The investigation strengthened practical skills in:

- Linux post-exploitation analysis
- Reverse-shell detection
- Credential discovery investigation
- Privilege-escalation analysis
- `auditd` investigation
- Systemd persistence detection
- Cron persistence detection
- Account persistence investigation
- SSH-key persistence detection
- Event correlation
- Incident timeline reconstruction

These techniques are directly applicable to SOC and incident-response investigations where analysts must determine not only **how an attacker gained access**, but also **how they escalated their privileges, what persistence mechanisms they established, and whether the attacker can still regain access after the initial incident is contained**.
