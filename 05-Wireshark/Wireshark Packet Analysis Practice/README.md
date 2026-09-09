# Wireshark Packet Analysis Practice

## Overview
This repository documents practical Wireshark exercises focused on network traffic analysis and packet investigation.

The exercises were completed using PCAP files in a simulated environment and focus on developing foundational skills relevant to SOC Level 1 monitoring and incident investigation.

The objective is not simply to identify answers, but to practice using Wireshark to investigate network activity, filter traffic, identify relevant packets, and interpret network communications.

---

## Objectives
* Analyze PCAP files using Wireshark
* Understand network traffic and communication patterns
* Identify hosts, endpoints, and conversations
* Analyze common network protocols
* Investigate DNS and HTTP traffic
* Create and use Wireshark display filters
* Use comparison and logical operators
* Identify potentially suspicious network activity
* Develop a structured packet-analysis workflow

---

## Tools
* **Wireshark**
* **PCAP / PCAPNG files**
* **TryHackMe** — *Wireshark: Packet Operations*

---

## Packet Analysis Workflow

```text
PCAP File
    │
    ▼
Traffic Overview
    │
    ▼
Protocol Analysis
    │
    ▼
Endpoints & Conversations
    │
    ▼
Identify Interesting Traffic
    │
    ▼
Apply Display Filters
    │
    ▼
Inspect Packets
    │
    ▼
Interpret Findings
    │
    ▼
Document Results
```
---

## 1. Traffic Statistics
Wireshark's **Statistics** menu provides a high-level overview of the traffic contained within a packet capture.

### Protocol Hierarchy
Used to identify the protocols present within the capture and understand the overall composition of the network traffic.

*Useful questions include:*
* What protocols are being used?
* Is HTTP traffic present?
* Is DNS traffic present?
* Are unexpected protocols present?

### Conversations
Used to identify communication between network endpoints.

*This can help identify:*
* Frequently communicating hosts
* High-volume connections
* Source and destination relationships
* Potentially unusual communications

### Endpoints
Used to identify individual network endpoints and their traffic statistics.

*Relevant information can include:*
* IP addresses
* MAC addresses
* Packet counts
* Bytes transferred

---

## 2. DNS Analysis
DNS traffic can provide useful information about systems communicating with other hosts and domains.

* **Display DNS Traffic:** `dns`
* **Identify DNS Queries:** `dns.flags.response == 0`

*Questions to investigate:*
* Which domains are being queried?
* Which host is generating the queries?
* Are there unusual domains?
* Are there unusually frequent DNS requests?

---

## 3. HTTP Analysis
HTTP traffic can provide valuable information about web-based communications.

* **Display HTTP Traffic:** `http`
* **Identify HTTP GET Requests:** `http.request.method == "GET"`
* **Identify Successful HTTP Responses:** `http.response.code == 200`

*Questions to investigate:*
* Which hosts are communicating over HTTP?
* What URLs are being requested?
* What HTTP methods are being used?
* Are files being downloaded?

---

## 4. IP Traffic Filtering
Wireshark display filters can be used to isolate traffic associated with specific hosts.

* **Display IP Traffic:** `ip`
* **Traffic Involving an IP Address:** `ip.addr == 10.10.10.111`
* **Traffic Originating From an IP:** `ip.src == 10.10.10.111`
* **Traffic Destined for an IP:** `ip.dst == 10.10.10.111`

> These filters are useful when investigating the activity of a particular host.

---

## 5. TCP and UDP Analysis

* **Display TCP Traffic:** `tcp`
* **Filter by TCP Port:** `tcp.port == 80`
* **Filter by Source Port:** `tcp.srcport == 1234`
* **Display UDP Traffic:** `udp`
* **DNS Traffic:** `udp.port == 53`

Filtering by protocol and port allows an analyst to narrow down large packet captures and focus on specific types of network communication.

---

## 6. Comparison Operators
Wireshark filters support comparison operators that can be used to identify specific packet characteristics.

*Examples:*
* `ip.ttl < 10`
* `tcp.port == 4444`
* `tcp.port != 80`

These operators can be useful when searching for unusual values or narrowing an investigation.

---

## 7. Logical Operators
Multiple conditions can be combined to create more targeted filters.

* **AND:** `ip.src == 10.10.10.111 && tcp.port == 80`
* **OR:** `tcp.port == 3333 || tcp.port == 4444`
* **NOT:** `!(tcp.port == 80)`

Combining conditions allows an analyst to progressively narrow down relevant traffic.

---

## 8. Example Investigation Filters
Some example filters practiced during the exercises include:

| Purpose | Filter |
| :--- | :--- |
| **HTTP GET Requests** | `http.request.method == "GET"` |
| **DNS Queries** | `dns.flags.response == 0` |
| **Low TTL Values** | `ip.ttl < 10` |
| **Multiple TCP Ports** | `tcp.port == 3333 \|\| tcp.port == 4444 \|\| tcp.port == 9999` |
| **Host + Protocol** | `ip.addr == 10.10.10.111 && http` |

---

## Skills Demonstrated
This project demonstrates practical experience with:
* Wireshark & PCAP analysis
* Network traffic analysis & protocol identification
* DNS & HTTP investigation
* TCP/IP traffic analysis
* Endpoint identification & network conversations
* Display filters, logical operators, and comparison operators
* Packet-level investigation

---

## SOC Relevance
Wireshark is an important tool for investigating network activity during security incidents. The skills practiced in this repository can be applied to SOC investigations involving:
* Suspicious network connections
* Malware traffic & Command-and-control communications
* DNS anomalies & suspicious HTTP requests
* Unusual ports & network reconnaissance
* Data transfer activity

> Wireshark can also be used alongside a SIEM such as Splunk to investigate network-related indicators identified during alert triage.

---

## Future Improvements
Future additions to this repository will focus on more security-oriented packet investigations, including:
* DNS threat hunting
* Port-scan detection
* Suspicious HTTP traffic
* Malware PCAP analysis
* Command-and-control traffic
* Network reconnaissance
* Data exfiltration analysis
* MITRE ATT&CK mapping
│
▼
Document Results
