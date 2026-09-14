# Indicators of Compromise (IOC) — Types & Categories

Indicators of Compromise (IOCs) are artifacts or pieces of information that can help identify potentially malicious or compromised activity.

## 1. Network IOCs

Indicators associated with network communications and infrastructure.

| Type                | Example                                              |
| ------------------- | ---------------------------------------------------- |
| **IP Address**      | `185.12.34.56`                                       |
| **Domain**          | `malicious-example.com`                              |
| **URL**             | `http://malicious-example.com/payload.exe`           |
| **Port**            | TCP `4444`                                           |
| **Protocol**        | Suspicious use of FTP, SSH, or SMB                   |
| **DNS Record**      | DNS request to a known malicious domain              |
| **Network Traffic** | Unexpected outbound connection to an external server |

**Example:**
A workstation repeatedly connects to a known malicious IP address over TCP/443.

---

## 2. File-Based IOCs

Indicators associated with files and malicious software.

| Type                  | Example                                      |
| --------------------- | -------------------------------------------- |
| **File Hash**         | SHA-256: `a1b2c3...`                         |
| **File Name**         | `invoice.exe`                                |
| **File Path**         | `C:\Users\Public\update.exe`                 |
| **File Extension**    | Unexpected `.exe`, `.dll`, `.ps1`, or `.bat` |
| **File Size**         | Unusually large or small executable          |
| **Digital Signature** | Missing or invalid signature                 |

### Common Hash Types

* **MD5**
* **SHA-1**
* **SHA-256**

**Example:**
An endpoint detects a file whose SHA-256 hash matches a known malware sample.

---

## 3. Host-Based IOCs

Indicators found directly on an endpoint or server.

| Type               | Example                                              |
| ------------------ | ---------------------------------------------------- |
| **Process**        | `powershell.exe`                                     |
| **Command Line**   | `powershell -enc ...`                                |
| **Service**        | Unexpected Windows service                           |
| **Scheduled Task** | `UpdateTask` running a suspicious executable         |
| **Registry Key**   | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` |
| **File/Directory** | Suspicious file in `%TEMP%`                          |
| **Driver**         | Unknown or unsigned driver                           |

**Example:**
A new scheduled task launches an executable from a user's temporary directory.

---

## 4. Account & Identity IOCs

Indicators involving user accounts and authentication activity.

| Type                    | Example                                     |
| ----------------------- | ------------------------------------------- |
| **Username**            | `john.smith`                                |
| **Email Address**       | `attacker@example.com`                      |
| **Failed Logins**       | Hundreds of failed authentication attempts  |
| **Successful Login**    | Login from an unusual location              |
| **Privilege Change**    | User added to Administrators group          |
| **New Account**         | Unexpected account creation                 |
| **Credential Activity** | Suspicious password reset or authentication |

**Example:**
An account normally used during business hours successfully logs in at 03:00 from an unusual location.

---

## 5. Email IOCs

Indicators commonly associated with phishing and malicious email activity.

| Type                 | Example                                  |
| -------------------- | ---------------------------------------- |
| **Sender Address**   | `support@paypa1-example.com`             |
| **Recipient**        | Targeted employee account                |
| **Subject**          | `Urgent: Account Verification Required`  |
| **Attachment**       | `Invoice_2026.exe`                       |
| **Attachment Hash**  | SHA-256 of malicious attachment          |
| **URL**              | Link to a phishing website               |
| **Reply-To Address** | Different from the sender domain         |
| **Email Domain**     | Lookalike domain such as `micros0ft.com` |

**Example:**
An email contains a link to a lookalike Microsoft login page.

---

## 6. Web IOCs

Indicators found in web server, application, or proxy activity.

| Type                | Example                                     |
| ------------------- | ------------------------------------------- |
| **URL**             | `/uploads/shell.php`                        |
| **IP Address**      | `171.251.232.40`                            |
| **User-Agent**      | `Hydra`                                     |
| **HTTP Method**     | Suspicious `POST` request                   |
| **URI**             | `/wp-login.php`                             |
| **Web Shell**       | `cmd.php`                                   |
| **HTTP Status**     | Unusual sequence of `404` / `500` responses |
| **Request Pattern** | SQL injection or command injection attempts |

**Example:**
A web server receives repeated POST requests attempting to upload or execute a PHP web shell.

---

## 7. Malware IOCs

Indicators specifically associated with malware.

| Type                      | Example                              |
| ------------------------- | ------------------------------------ |
| **Malware Hash**          | Known malicious SHA-256              |
| **Malware File**          | `payload.exe`                        |
| **C2 Domain**             | `command-control.example`            |
| **C2 IP**                 | `203.0.113.50`                       |
| **Mutex**                 | Malware-specific mutex name          |
| **Malware Process**       | Suspicious process spawned by Office |
| **Persistence Mechanism** | Registry Run key or scheduled task   |

**Example:**
A workstation executes a known malware hash and subsequently connects to a known C2 domain.

---

## 8. Behavioral IOCs

Indicators based on suspicious activity rather than a specific artifact.

| Type                     | Example                                        |
| ------------------------ | ---------------------------------------------- |
| **Brute Force**          | Repeated failed login attempts                 |
| **Privilege Escalation** | Normal user suddenly gains admin privileges    |
| **Lateral Movement**     | SMB connections to multiple workstations       |
| **Data Exfiltration**    | Large outbound data transfer                   |
| **Persistence**          | Creation of a scheduled task                   |
| **Discovery**            | `whoami`, `ipconfig`, or network enumeration   |
| **Execution**            | Suspicious PowerShell or command-line activity |

Behavioral indicators are particularly useful because attackers can change specific IOCs such as IP addresses and file hashes, while their **attack techniques and behaviors may remain similar**.

---

# Quick IOC Reference

| Category          | Common IOCs                                              |
| ----------------- | -------------------------------------------------------- |
| 🌐 **Network**    | IPs, domains, URLs, ports, DNS                           |
| 📁 **File**       | Hashes, filenames, paths, extensions                     |
| 💻 **Host**       | Processes, services, registry, scheduled tasks           |
| 👤 **Identity**   | Users, logins, privileges, account changes               |
| ✉️ **Email**      | Senders, attachments, URLs, domains                      |
| 🌍 **Web**        | URLs, HTTP requests, User-Agents, web shells             |
| 🦠 **Malware**    | Hashes, C2 infrastructure, mutexes, payloads             |
| ⚠️ **Behavioral** | Brute force, lateral movement, persistence, exfiltration |

## Key Point

> **IOCs can be technical artifacts or observable behaviors that help analysts identify potentially malicious activity.**

