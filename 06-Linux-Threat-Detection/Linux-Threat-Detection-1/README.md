# Linux Threat Detection 1 — Initial Access Investigation

## Overview

This investigation focuses on identifying and analysing **initial access activity against a Linux system**.

The investigation examines two common attack paths:

- Compromise through **SSH**
- Compromise through an **internet-facing application**

Linux authentication logs, application activity and audit telemetry were analysed to identify malicious login attempts, determine whether an account had been successfully compromised, investigate suspicious command execution and reconstruct the process chain responsible for the activity.

The main objective was to understand how individual events can be correlated to distinguish normal administrative activity from genuine malicious behaviour.

---

# Investigation Objectives

The investigation focused on the following objectives:

- Analyse Linux SSH authentication activity
- Identify repeated failed authentication attempts
- Detect SSH brute-force behaviour
- Determine whether brute-force activity resulted in successful compromise
- Identify the affected account and attacker source
- Analyse suspicious activity involving an internet-facing application
- Investigate command execution using Linux audit logs
- Identify parent and child process relationships
- Reconstruct a malicious process tree
- Determine the original source of suspicious command execution
- Identify behaviour consistent with a reverse shell
- Correlate multiple events into a single attack sequence

---

# Skills Demonstrated

- Linux security log analysis
- SSH authentication investigation
- Brute-force detection
- Successful-login correlation
- `/var/log/auth.log` analysis
- Linux `auditd` analysis
- `ausearch`
- Command-line analysis
- Parent/child process investigation
- Process-tree reconstruction
- Application compromise investigation
- Reverse-shell detection
- Initial-access analysis
- Event correlation
- Incident timeline reconstruction

---

# Data Sources & Tools

| Source / Tool | Purpose |
| :--- | :--- |
| `/var/log/auth.log` | Analyse SSH authentication and account activity |
| Application logs | Identify suspicious requests and potential exploitation |
| `auditd` | Record process execution and system activity |
| `ausearch` | Search and interpret audit events |
| `grep` | Filter relevant log entries |
| `cat` | Review log files and system data |
| Process IDs | Reconstruct process relationships |
| Parent Process IDs | Identify the process responsible for launching suspicious activity |

---

# 1. SSH Authentication Analysis

The investigation began by reviewing SSH authentication activity.

Linux systems commonly record SSH authentication events inside:

```bash
/var/log/auth.log
```

Relevant SSH activity can be extracted using:

```bash
grep "sshd" /var/log/auth.log
```

Authentication logs can contain several important fields, including:

```text
Timestamp
Hostname
SSH process
Username
Authentication result
Source IP address
Source port
Authentication method
```

These fields provide the information required to determine **who attempted to authenticate, where the connection originated and whether the attempt succeeded**.

---

# 2. Establishing Normal SSH Activity

Before identifying malicious behaviour, it is useful to establish what legitimate authentication looks like.

A successful SSH login may appear similar to:

```text
sshd[1842]: Accepted publickey for ubuntu from 192.168.1.25 port 51244 ssh2
```

Important information from this event includes:

```text
Result: Accepted
User: ubuntu
Source IP: 192.168.1.25
Authentication Method: Public Key
Service: SSH
```

This event alone does not indicate malicious activity.

The analyst should consider:

- Whether the account normally uses SSH
- Whether the source IP is expected
- Whether the authentication method is normal
- Whether the login time is unusual
- What activity occurred after authentication

This creates a baseline against which suspicious behaviour can be compared.

---

# 3. Detecting Failed SSH Authentication

Failed SSH authentication attempts can be identified using:

```bash
grep "Failed password" /var/log/auth.log
```

Example:

```text
sshd[2314]: Failed password for root from 203.0.113.45 port 44581 ssh2
sshd[2316]: Failed password for root from 203.0.113.45 port 44582 ssh2
sshd[2319]: Failed password for root from 203.0.113.45 port 44583 ssh2
sshd[2322]: Failed password for root from 203.0.113.45 port 44584 ssh2
```

This activity immediately provides several useful indicators:

```text
Target Account: root
Source IP: 203.0.113.45
Authentication Method: Password
Result: Failed
Pattern: Repeated authentication attempts
```

A single failed login is common and may simply indicate an incorrect password.

Repeated failures against the same account from the same source, however, may indicate a **password brute-force attack**.

---

# 4. Identifying SSH Brute-Force Behaviour

A brute-force attack attempts multiple passwords against an account until the correct credential is discovered.

The investigation revealed repeated authentication attempts targeting the same user account.

A simplified sequence may appear as:

```text
Failed login
    ↓
Failed login
    ↓
Failed login
    ↓
Failed login
    ↓
Failed login
```

The following factors increase the likelihood that the activity is malicious:

- Large number of authentication failures
- Attempts occurring within a short time period
- Same source IP repeatedly targeting the system
- Repeated attempts against privileged accounts
- Sequential attempts across multiple usernames
- Successful authentication occurring after numerous failures

The last point is especially important.

Repeated failures demonstrate an **attempted attack**.

A successful login following those failures may indicate a **successful compromise**.

---

# 5. Successful Authentication After Repeated Failures

Successful SSH authentication events can be identified using:

```bash
grep "Accepted" /var/log/auth.log
```

Example:

```text
sshd[2441]: Accepted password for root from 203.0.113.45 port 45122 ssh2
```

When correlated with the earlier failed authentication attempts, the activity becomes significantly more suspicious.

The sequence now appears as:

```text
203.0.113.45
      │
      ├── Failed authentication
      ├── Failed authentication
      ├── Failed authentication
      ├── Failed authentication
      │
      └── Successful authentication
                     │
                     ▼
                  root
```

The critical finding is not simply that a successful login occurred.

The important finding is that:

> **The same source responsible for repeated failed authentication attempts eventually authenticated successfully to the targeted account.**

This provides strong evidence that the brute-force attack succeeded.

---

# 6. Authentication Correlation

Authentication events should rarely be investigated independently.

Consider the following event:

```text
Accepted password for root
```

On its own, this only tells us that the account authenticated successfully.

Now consider the surrounding events:

```text
10:15:21 Failed password for root from 203.0.113.45
10:15:24 Failed password for root from 203.0.113.45
10:15:27 Failed password for root from 203.0.113.45
10:15:31 Failed password for root from 203.0.113.45
10:15:35 Accepted password for root from 203.0.113.45
```

The context changes the interpretation completely.

Instead of:

```text
Successful SSH login
```

the analyst can now identify:

```text
SSH brute-force activity
        ↓
Credential discovery
        ↓
Successful authentication
        ↓
Potential account compromise
```

This demonstrates the importance of **event correlation** during incident investigation.

---

# 7. Investigating Activity After Authentication

After determining that an attacker may have successfully authenticated, the next question is:

> **What did the attacker do after gaining access?**

Post-authentication activity may include:

```text
whoami
id
hostname
uname
pwd
ls
ps
cat
wget
curl
bash
```

None of these commands are inherently malicious.

Their significance depends on:

- Which account executed them
- When they were executed
- Which process launched them
- Whether the behaviour matches normal system administration
- Whether they occurred immediately after suspicious authentication

For example:

```text
Brute-force activity
        ↓
Successful root login
        ↓
whoami
        ↓
hostname
        ↓
ps
```

would strongly suggest that the attacker began performing **system discovery** after gaining access.

---

# 8. Application-Based Initial Access

SSH was not the only initial-access path investigated.

The investigation also examined suspicious command execution originating from an internet-facing application.

An exposed or vulnerable application can provide attackers with a path into the underlying operating system.

The analyst may initially observe a suspicious command such as:

```bash
whoami
```

The command itself does not prove malicious activity.

The important question is:

> **Which process caused `whoami` to execute?**

---

# 9. Why Process Context Matters

Consider the following examples.

### Normal behaviour

```text
SSH Session
    ↓
Bash
    ↓
whoami
```

An administrator running `whoami` interactively is entirely normal.

### Suspicious behaviour

```text
Web Application
      ↓
Shell
      ↓
whoami
```

A web application spawning a shell that executes `whoami` is significantly more suspicious.

This could indicate:

- Command injection
- Remote code execution
- Web-shell activity
- Application compromise

This is why Linux process relationships are extremely useful during threat detection.

---

# 10. Using auditd for Process Investigation

Linux `auditd` provides detailed information about system events and process execution.

A suspicious command can be investigated using:

```bash
ausearch -i -x whoami
```

This search may reveal information including:

```text
Executable
Command
Process ID
Parent Process ID
User ID
Timestamp
Terminal
Working directory
```

The two most important fields for reconstructing activity are:

```text
pid
ppid
```

where:

```text
PID  = Process ID
PPID = Parent Process ID
```

The PPID identifies the process responsible for launching the suspicious command.

---

# 11. Reconstructing the Process Tree

Suppose an audit event reveals:

```text
Command: whoami
PID: 4102
PPID: 4098
```

The parent process can then be investigated.

If PID `4098` corresponds to:

```text
/bin/sh
```

the current process chain becomes:

```text
/bin/sh
   ↓
whoami
```

The investigation should then continue by identifying the parent process of `/bin/sh`.

For example:

```text
Web Application
      ↓
/bin/sh
      ↓
whoami
```

This identifies the application as the original source of the suspicious command execution.

---

# 12. Parent/Child Process Analysis

Process-tree investigation allows the analyst to move from:

```text
What happened?
```

to:

```text
Why did it happen?
```

For example:

```text
whoami
```

tells the analyst that a command was executed.

However:

```text
Web Server
    ↓
Application Process
    ↓
Shell
    ↓
whoami
```

provides much more useful information.

It indicates that:

1. The web-facing application was involved.
2. The application launched a shell.
3. The shell executed an operating-system command.
4. The behaviour may indicate exploitation of the application.

This makes process-tree reconstruction a powerful technique for identifying **initial access and execution activity**.

---

# 13. Suspicious Shell Execution

A web application launching a shell should receive immediate attention.

Potential shell processes include:

```text
/bin/sh
/bin/bash
dash
bash
```

A suspicious chain might look similar to:

```text
Web Application
      ↓
/bin/sh
      ↓
bash
```

The analyst should then investigate the commands executed by the shell.

Possible indicators include:

```text
whoami
hostname
uname
id
wget
curl
nc
socat
python
bash
```

The combination of these commands can indicate attacker reconnaissance or preparation for further exploitation.

---

# 14. Reverse-Shell Behaviour

After gaining command execution through an application, an attacker may attempt to obtain a more interactive shell.

This is commonly achieved through a **reverse shell**.

Instead of the attacker connecting directly to a shell listening on the victim, the compromised system initiates the connection back to the attacker.

The behaviour can be represented as:

```text
Attacker
   ▲
   │
Outbound Connection
   │
Compromised Linux Host
   ▲
   │
Shell Process
   ▲
   │
Vulnerable Application
```

Utilities commonly associated with reverse-shell activity include:

```text
nc
netcat
socat
bash
python
perl
php
```

These tools are not inherently malicious.

However, they become suspicious when they appear inside an unexpected process chain.

---

# 15. Example Reverse-Shell Process Chain

A suspicious process tree could appear as:

```text
Web Server
    ↓
Application
    ↓
/bin/sh
    ↓
socat
    ↓
External Connection
```

This sequence provides substantially stronger evidence of compromise than simply observing `socat` running on the host.

The context shows:

- An external-facing application initiated the activity
- A shell was spawned
- A networking utility was executed
- An external connection was established

Together, these indicators are consistent with **remote shell activity**.

---

# 16. Investigation Timeline

The investigation demonstrated two potential initial-access paths.

## SSH Attack Path

```text
External Source
      ↓
Repeated SSH Authentication Failures
      ↓
Password Brute Force
      ↓
Successful Authentication
      ↓
Privileged Account Access
      ↓
Post-Login Activity
```

## Application Exploitation Path

```text
External Request
      ↓
Internet-Facing Application
      ↓
Command Execution
      ↓
Shell Spawned
      ↓
System Discovery
      ↓
Reverse-Shell Activity
```

These paths demonstrate that attackers can obtain Linux access through both **remote authentication services** and **vulnerable applications**.

---

# 17. Key Findings

| Finding | Assessment |
| :--- | :--- |
| Repeated SSH authentication failures | Suspicious |
| Privileged account targeted | High risk |
| Successful authentication following repeated failures | Strong indication of compromise |
| Same source involved in failures and success | Correlated malicious activity |
| Application spawning a shell | Highly suspicious |
| System commands executed by application child process | Potential remote code execution |
| Networking utility launched from shell | Possible reverse shell |
| Suspicious process chain originating from internet-facing service | Strong compromise indicator |

---

# 18. Indicators of Compromise

Potential indicators identified during this investigation included:

### Authentication Indicators

```text
Repeated failed SSH authentication
Successful login after repeated failures
Unexpected privileged-account login
Unfamiliar external source IP
Password authentication against sensitive accounts
```

### Process Indicators

```text
Web application spawning /bin/sh
Web application spawning /bin/bash
Unexpected execution of discovery commands
Suspicious parent/child relationships
```

### Network / Execution Indicators

```text
nc
netcat
socat
bash network redirection
Unexpected outbound connections from application processes
```

These indicators should always be evaluated together with environmental context.

---

# 19. Analyst Reasoning

The key investigative principle throughout this analysis was:

> **Do not classify events based solely on the command or log entry. Investigate the surrounding context.**

For example:

```text
whoami
```

is not malicious.

Similarly:

```text
Successful SSH login
```

is not automatically malicious.

However:

```text
Repeated authentication failures
        ↓
Successful root authentication
        ↓
System discovery
```

is significantly more concerning.

Likewise:

```text
Web application
      ↓
Shell
      ↓
whoami
      ↓
Network utility
      ↓
External connection
```

provides a clear indication that the observed commands are part of potentially malicious activity.

---

# 20. Key SOC Lessons

## Authentication Events Require Correlation

Successful logins must be analysed together with preceding authentication activity.

A successful login following repeated failed attempts from the same source is substantially more suspicious than an isolated successful login.

---

## Commands Are Not Malicious by Themselves

Utilities such as:

```text
whoami
bash
curl
wget
socat
```

are legitimate tools.

Detection should focus on:

```text
User
+
Parent Process
+
Command
+
Timestamp
+
Network Activity
+
Surrounding Events
```

---

## Process Trees Provide Critical Evidence

Understanding parent/child relationships can reveal how suspicious activity entered the system.

A process tree can transform:

```text
whoami executed
```

into:

```text
Internet-facing application
        ↓
Shell
        ↓
whoami
```

which provides much stronger evidence.

---

## Initial Access Is Only the Beginning

Once an attacker obtains access, the investigation should continue.

The analyst should determine:

- Which account was compromised
- Which commands were executed
- Whether additional tools were downloaded
- Whether privilege escalation occurred
- Whether persistence was established
- Whether external systems were contacted

This prevents the investigation from stopping at the first alert.

---

# Conclusion

This investigation demonstrated two important methods attackers can use to gain initial access to Linux systems: **SSH credential attacks and exploitation of internet-facing applications**.

Authentication-log analysis showed how repeated SSH failures can be correlated with a later successful login to identify a potential brute-force compromise.

The investigation also demonstrated the importance of Linux audit telemetry when suspicious commands originate from an application. By examining process IDs and parent process IDs, it was possible to trace command execution back through the process tree and identify the application responsible for launching the activity.

The most important lesson was that individual events rarely provide enough context on their own.

A failed login, successful authentication, `whoami` execution or networking utility may all appear relatively insignificant when viewed independently. When correlated, however, they can reveal a complete initial-access sequence.

The investigation strengthened practical skills in:

- Linux authentication analysis
- SSH brute-force detection
- Initial-access investigation
- `auditd` analysis
- Process-tree reconstruction
- Parent/child process correlation
- Application compromise detection
- Reverse-shell identification
- Incident timeline reconstruction

These techniques are directly applicable to SOC investigations where analysts must determine **whether activity is malicious, how access was obtained, which accounts or applications were affected, and what the attacker did after gaining access**.
