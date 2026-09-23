# Cyber Kill Chain

The **Cyber Kill Chain** is a framework developed by **Lockheed Martin** that describes the typical stages an attacker may follow during a cyberattack. It can be used by SOC analysts to understand an attacker's progress and identify opportunities to detect, disrupt, or contain malicious activity.

## 7 Stages of the Cyber Kill Chain

| Stage                         | Description                                                                                                                    | SOC Analyst Focus                                                                                     |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| **1. Reconnaissance**         | The attacker gathers information about the target, such as domains, IP addresses, employees, and technologies.                 | Identify scanning, enumeration, and suspicious information gathering.                                 |
| **2. Weaponization**          | The attacker prepares a malicious payload, such as malware combined with an exploit or document.                               | Analyze malware, files, attachments, and known indicators.                                            |
| **3. Delivery**               | The malicious payload is delivered to the target, commonly through phishing emails, malicious links, or compromised websites.  | Monitor email security, web traffic, and suspicious downloads.                                        |
| **4. Exploitation**           | The attacker exploits a vulnerability or uses a user interaction to execute the malicious payload.                             | Investigate exploit attempts, abnormal processes, and application behavior.                           |
| **5. Installation**           | Malware or another persistence mechanism is installed on the compromised system.                                               | Look for new services, scheduled tasks, registry changes, and suspicious files.                       |
| **6. Command & Control (C2)** | The compromised system communicates with attacker-controlled infrastructure.                                                   | Investigate unusual outbound connections, DNS requests, IP addresses, and domains.                    |
| **7. Actions on Objectives**  | The attacker performs their intended actions, such as stealing data, deploying ransomware, or compromising additional systems. | Determine the impact, investigate affected systems/accounts, and support containment and remediation. |

## Why It Matters in a SOC

The Cyber Kill Chain helps SOC analysts **break an attack into identifiable stages** rather than viewing an incident as a single event.

For example:

**Phishing Email → Exploitation → Malware Installation → C2 → Data Exfiltration**

If the SOC detects the attack during the **Delivery** or **Exploitation** stage, analysts may be able to prevent the attacker from reaching later stages.

### Key Takeaway

The main purpose of the Cyber Kill Chain is to provide a structured way to understand an attack and identify **where defensive controls can detect or disrupt the attacker**.

> **Earlier detection = fewer opportunities for the attacker to progress through the kill chain.**
