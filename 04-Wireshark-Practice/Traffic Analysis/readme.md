# Wireshark Traffic Analysis

## Overview

This repository documents practical **network traffic analysis and threat detection exercises using Wireshark**.

The project focuses on analyzing PCAP files to identify suspicious network activity, recognize attack patterns, investigate potentially malicious communications, and extract actionable security information.

The exercises are based on the **TryHackMe — Wireshark: Traffic Analysis** learning environment and are presented from a **SOC analyst perspective** rather than as a walkthrough of the lab questions.

---

## Objectives

* Analyze network traffic using Wireshark
* Identify common network reconnaissance techniques
* Detect Nmap scanning activity
* Investigate ARP poisoning and Man-in-the-Middle activity
* Analyze DNS and ICMP tunneling
* Investigate suspicious FTP activity
* Identify malicious HTTP traffic
* Analyze potentially malicious User-Agent strings
* Investigate Log4j exploitation patterns
* Analyze encrypted HTTPS traffic
* Decrypt TLS traffic using a key log file
* Identify cleartext credentials
* Convert findings into actionable security controls

---

## Tools

* **Wireshark**
* **PCAP / PCAPNG files**
* **TryHackMe — Wireshark: Traffic Analysis**

---

# Investigation Workflow

The following workflow was used throughout the exercises:

```text
                PCAP
                  │
                  ▼
        Traffic Overview
                  │
                  ▼
        Identify Protocols
                  │
                  ▼
     Identify Hosts & Endpoints
                  │
                  ▼
       Detect Anomalous Traffic
                  │
                  ▼
         Apply Wireshark Filters
                  │
                  ▼
       Inspect Packet Details
                  │
                  ▼
        Correlate Evidence
                  │
                  ▼
       Determine Attack Pattern
                  │
                  ▼
       Document Findings
                  │
                  ▼
     Recommend Security Controls
```

---

# 1. Nmap Scan Detection

Network scanning is commonly used during reconnaissance to identify accessible hosts and services.

Wireshark can be used to identify scanning activity by analyzing TCP flags, connection patterns, port activity, and ICMP responses.

## TCP Connect Scan

TCP Connect scans complete the TCP three-way handshake.

Example filter:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.window_size > 1024
```

The TCP conversation can then be examined to determine whether the connection proceeds through:

```text
SYN
 ↓
SYN/ACK
 ↓
ACK
```

A completed handshake is consistent with a TCP Connect scan.

## SYN Scan

SYN scans are commonly associated with half-open connections.

Example filter:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.window_size <= 1024
```

The analyst can investigate whether the target responds with:

```text
SYN/ACK
```

followed by:

```text
RST
```

rather than a completed TCP connection.

## UDP Scan Detection

Closed UDP ports commonly generate ICMP Destination Unreachable responses.

Example filter:

```text
icmp.type == 3 && icmp.code == 3
```

This can be used to identify potential UDP scanning activity.

### SOC Relevance

Network scanning can represent:

* Reconnaissance
* Service discovery
* Vulnerability assessment
* Internal network enumeration

Repeated connections across many ports or hosts should be investigated in context.

---

# 2. ARP Poisoning & Man-in-the-Middle Detection

ARP does not provide authentication, making it susceptible to spoofing attacks.

During an ARP poisoning attack, an attacker may attempt to associate their MAC address with another host's IP address.

### Investigation Focus

The investigation focused on:

* ARP requests and replies
* IP-to-MAC relationships
* Duplicate or conflicting mappings
* Unexpected changes in ARP information
* Traffic being redirected through an attacker

Potential indicators include multiple devices claiming ownership of the same IP address.

### SOC Relevance

Successful ARP poisoning can allow an attacker to:

* Intercept network traffic
* Capture credentials
* Modify communications
* Perform Man-in-the-Middle attacks
* Monitor unencrypted protocols

---

# 3. Host & User Identification

Network traffic can contain information that helps identify systems and users.

Protocols investigated included:

* DHCP
* NetBIOS
* Kerberos

These protocols can provide useful information such as:

* Hostnames
* IP addresses
* MAC addresses
* Usernames
* Network configuration

This information can help an analyst build an understanding of the environment before investigating suspicious activity.

---

# 4. DNS & ICMP Tunneling

Network tunneling can allow attackers to hide communications inside protocols that are normally permitted through network controls.

## ICMP Tunneling

Large ICMP packets can be investigated for unusual payload data.

Example filter:

```text
icmp && data.len > 64
```

Large or unusual ICMP payloads may warrant further investigation.

Packet contents can then be inspected to determine whether the data appears to contain another protocol or communication stream.

## DNS Tunneling

DNS can also be abused as a covert communication channel.

Useful filter:

```text
dns
```

Detect unusually long DNS queries:

```text
dns.qry.name.len > 15 && !mdns
```

Search for potential tunneling indicators:

```text
dns contains "dnscat"
```

Potential indicators include:

* Very long DNS queries
* High-frequency DNS requests
* Random-looking subdomains
* Large volumes of TXT queries
* Repeated communication with a suspicious domain

### SOC Relevance

DNS and ICMP tunneling can potentially be used for:

* Command and control
* Data exfiltration
* Covert communications
* Bypassing network restrictions

---

# 5. FTP Traffic Analysis

FTP traffic can expose authentication and file-transfer activity.

The investigation focused on:

* Failed login attempts
* Successful authentication
* File transfers
* Uploaded files
* FTP commands
* File permissions

Useful display filters include:

```text
ftp
```

and:

```text
ftp.request.command
```

FTP commands can provide valuable insight into attacker behavior.

Examples include:

```text
USER
PASS
RETR
STOR
SITE
```

### SOC Relevance

Suspicious FTP activity may indicate:

* Brute-force attempts
* Unauthorized access
* Data theft
* Malware uploads
* File manipulation

---

# 6. HTTP Traffic Analysis

HTTP traffic can provide valuable information because the protocol may expose requests, headers, parameters, and other application-layer data.

Example filter:

```text
http
```

HTTP GET requests:

```text
http.request.method == "GET"
```

HTTP POST requests:

```text
http.request.method == "POST"
```

## User-Agent Analysis

The HTTP User-Agent field can provide information about the client generating a request.

Suspicious or unusual User-Agent strings may indicate:

* Automated scanning
* Security testing tools
* Malware
* Command-line tools
* Attacker-controlled traffic

Example filter:

```text
http.user_agent contains "Nmap"
```

Multiple tool indicators can be combined:

```text
http.user_agent contains "Nmap" ||
http.user_agent contains "sqlmap"
```

---

# 7. Log4j Attack Detection

The investigation also included analysis of HTTP traffic associated with potential Log4j exploitation.

Example filter:

```text
http.request.method == "POST"
```

Search for suspicious strings:

```text
frame contains "jndi"
```

or:

```text
frame contains "Exploit"
```

Potential exploitation attempts can be identified by correlating:

* HTTP request method
* Request parameters
* User-Agent
* Payload contents
* Destination server
* Timing and frequency

### SOC Relevance

Packet-level detection can help identify exploitation attempts even when higher-level application logs are unavailable.

---

# 8. HTTPS & TLS Analysis

Encrypted traffic presents an additional challenge because the contents of the application-layer communication are normally hidden.

The investigation covered the use of TLS metadata and session keys to decrypt captured HTTPS traffic.

### TLS Client Hello

A useful filter for identifying TLS Client Hello messages is:

```text
tls.handshake.type == 1
```

The TLS handshake can then be inspected for information such as:

* Server Name Indication (SNI)
* TLS version
* Cipher suites
* Extensions
* Destination hostname

---

# 9. HTTPS Decryption

When the appropriate TLS key log file is available, Wireshark can decrypt supported TLS traffic.

The investigation process involved:

```text
PCAP
 │
 ▼
TLS Handshake
 │
 ▼
Identify Target Session
 │
 ▼
Load TLS Key Log
 │
 ▼
Wireshark TLS Decryption
 │
 ▼
Inspect Decrypted Traffic
 │
 ▼
Investigate Application Data
```

This allows an analyst to inspect traffic that would otherwise remain encrypted.

### SOC Relevance

TLS decryption can assist investigations involving:

* Suspicious web traffic
* Command and control
* Malware communications
* Credential theft
* Data exfiltration

In real environments, decryption capabilities depend on available keys, architecture, privacy requirements, and organizational security controls.

---

# 10. Cleartext Credential Hunting

Unencrypted protocols can expose credentials directly within captured traffic.

The investigation included identifying authentication information transmitted using HTTP Basic Authentication.

Example filter:

```text
http.authorization
```

Credentials should be treated as compromised if they are observed in an unauthorized packet capture.

### Security Impact

Cleartext credentials can allow an attacker who can observe network traffic to obtain:

* Usernames
* Passwords
* Session information

This highlights the importance of encrypted protocols such as HTTPS and secure authentication mechanisms.

---

# 11. Actionable Security Results

Packet analysis should not stop at identifying suspicious traffic.

The final stage of an investigation is determining what action could be taken to reduce the risk.

Examples include:

* Blocking malicious IP addresses
* Blocking suspicious MAC addresses
* Restricting malicious network traffic
* Investigating compromised hosts
* Improving IDS/IPS detections
* Creating firewall rules

This demonstrates the transition from:

```text
Packet
   ↓
Evidence
   ↓
Finding
   ↓
Threat Assessment
   ↓
Security Action
```

---

# Detection Techniques

| Activity              | Example Indicator            | Wireshark Technique     |
| --------------------- | ---------------------------- | ----------------------- |
| Nmap scanning         | Repeated SYN packets         | TCP flag analysis       |
| UDP scanning          | ICMP port unreachable        | ICMP filtering          |
| ARP poisoning         | Conflicting IP/MAC mappings  | ARP analysis            |
| DNS tunneling         | Long/random queries          | DNS filtering           |
| ICMP tunneling        | Large ICMP payloads          | ICMP + payload analysis |
| FTP attack            | Failed logins / file uploads | FTP analysis            |
| HTTP reconnaissance   | Suspicious User-Agent        | HTTP filtering          |
| Log4j exploitation    | `jndi` / exploit strings     | Payload inspection      |
| Suspicious HTTPS      | Unusual TLS metadata         | TLS analysis            |
| Cleartext credentials | Authorization headers        | HTTP analysis           |

---

# SOC Investigation Skills Demonstrated

This project demonstrates practical experience with:

* Wireshark
* PCAP analysis
* Network traffic analysis
* Nmap detection
* TCP flag analysis
* UDP scanning detection
* ARP poisoning investigation
* Man-in-the-Middle analysis
* DHCP analysis
* NetBIOS analysis
* Kerberos traffic analysis
* DNS analysis
* DNS tunneling detection
* ICMP tunneling detection
* FTP investigation
* HTTP analysis
* User-Agent analysis
* Log4j attack detection
* TLS analysis
* HTTPS decryption
* Credential exposure detection
* Firewall rule generation

---

# SOC Relevance

Wireshark can provide valuable packet-level evidence during a security investigation.

This project demonstrates the ability to move beyond simply viewing packets and instead use network traffic to answer security questions:

> **What happened?**

> **Which systems were involved?**

> **How did the activity occur?**

> **What indicators can be identified?**

> **Is the activity potentially malicious?**

> **What action should be taken?**

These are fundamental questions during SOC alert triage and network-based incident investigations.

---

# Future Improvements

Future investigations will expand this repository into more advanced network-security scenarios, including:

* Malware PCAP analysis
* Command-and-control detection
* Network reconnaissance
* Data exfiltration
* Suspicious DNS investigation
* HTTP malware traffic
* Beaconing detection
* Brute-force detection
* Lateral movement analysis
* MITRE ATT&CK mapping
* Correlation of Wireshark findings with SIEM alerts

---
