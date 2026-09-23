# Wireshark Practice

This section documents practical **network traffic analysis and packet investigation using Wireshark and PCAP files**.

The exercises focus on developing foundational skills relevant to SOC monitoring and network-based incident investigation, including traffic filtering, protocol analysis, reconnaissance detection, tunnelling, suspicious web activity, and credential exposure.

---

## Labs

| Lab | Focus | Link |
| :--- | :--- | :--- |
| **Packet Analysis Practice** | Wireshark fundamentals, packet inspection, endpoints, conversations, DNS, HTTP, and display filtering. | [View Packet Analysis](Packet%20Analysis%20Practice/) |
| **Traffic Analysis** | Security-focused network investigation covering scanning, tunnelling, suspicious protocols, malicious web traffic, and TLS analysis. | [View Traffic Analysis](Traffic%20Analysis/) |

---

## Packet Analysis Practice

This lab focuses on using Wireshark to inspect and understand captured network traffic.

Topics include:

- PCAP and PCAPNG analysis
- Traffic statistics
- Endpoint identification
- Conversation analysis
- Protocol inspection
- DNS traffic analysis
- HTTP traffic analysis
- Wireshark display filters
- Comparison and logical operators
- Identifying potentially suspicious traffic

The exercise follows a structured packet-analysis workflow:

**Traffic Overview → Protocol Analysis → Endpoints & Conversations → Filtering → Packet Inspection → Findings**

[View Packet Analysis Practice](Packet%20Analysis%20Practice/)

---

## Traffic Analysis

This lab focuses on identifying suspicious and potentially malicious network behavior.

Investigation topics include:

- Nmap scanning activity
- Network reconnaissance
- ARP poisoning
- Man-in-the-Middle activity
- DNS tunnelling
- ICMP tunnelling
- Suspicious FTP activity
- Malicious HTTP traffic
- User-Agent analysis
- Log4j exploitation indicators
- HTTPS traffic analysis
- TLS decryption using key-log data
- Cleartext credential exposure

The goal is to move beyond basic packet inspection and use captured network traffic to identify **attack patterns and actionable security findings**.

[View Traffic Analysis](Traffic%20Analysis/)

---

## Skills Demonstrated

- Wireshark
- PCAP analysis
- Network traffic investigation
- TCP/IP analysis
- DNS analysis
- HTTP / HTTPS analysis
- Protocol identification
- Display filtering
- Network reconnaissance detection
- Tunnelling detection
- Suspicious traffic identification
- Packet-level incident investigation

---

## Tools & Data

- **Wireshark**
- **PCAP / PCAPNG files**
- TCP/IP
- DNS
- HTTP / HTTPS
- FTP
- ICMP
- ARP

---

## Analysis Approach

The exercises use a structured network-investigation workflow:

**Capture Review → Traffic Baseline → Filtering → Protocol Analysis → Suspicious Activity Identification → Findings**

All analysis was performed in **simulated cybersecurity training environments**.
