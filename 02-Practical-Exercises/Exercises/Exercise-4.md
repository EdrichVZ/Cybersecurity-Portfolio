<img width="784" height="1134" alt="Exercise 4" src="https://github.com/user-attachments/assets/a42c47d7-c90a-4019-bb7e-135f689e086c" />

# SOC Alert & IOC Analysis

## Task 1

* **Alert 1** shows **`j.alvares`** established and successfully authenticated into the internal network outside normal business hours. This is **anomalous user account activity** and potentially **unauthorized access**, which is a **Host-related IOC**.

* **Alert 2** shows **Outlook** spawning a **PowerShell** process, which isn't normal. This PowerShell process executed a **Base64-encoded, obfuscated command** that added a new registry value to change a Windows startup setting. This allows malware or another malicious program to restart every time the machine is turned on. The script also dropped a file called **`syshelp.dll`** into a public folder and executed it, which started **`rundll32.exe`**. This is a **Host-based IOC**.

* **Alert 3** shows **`RAD-WKS-22`** actively beaconing to an external source every **30 seconds**. It then shows **`RAD-WKS-22`** connecting to an internal file server and transferring **4.2 GB** of data to the internal file server **`FILE-SRV-03`** using **SMB**, potentially staging data for **data exfiltration**. This is indicative of **C2 (Command and Control)** activity, which is a **Network-related IOC**.

## Task 2

**Authentication** — If **Multi-Factor Authentication (MFA)** was implemented correctly, the attacker would not have been able to log in. If an **inherence factor**, such as biometrics, was implemented, the attacker could not have succeeded with stolen credentials alone.

## Task 3

It’s a scale used by analysts to determine how confident they are that a threat is actually dangerous, based on **accurate, logical, and external data**.

A **Probable** score is between **70–89**, meaning that the threat is highly plausible and supported by available information.

Knowing this, I would probably **block `198.51.100.7`** from the corporate network.

## Task 4

**`FILE-SRV-03`** has a **CVSS score of 9.1**, which means the vulnerability needs to be remediated immediately. A score as high as this can indicate the potential for **complete system takeover or remote code execution**.

In this incident, it seems that the threat actor has probably already taken over the file server **`FILE-SRV-03`** and is preparing a large-scale **data exfiltration**.

Knowing this, both **`RAD-WKS-22`** and **`FILE-SRV-03`** need to be **isolated immediately** to cut off the **C2 (Command and Control)** communication.

After isolation, the **eradication of malicious software** needs to take place, and the necessary **security patches** need to be applied so that the vulnerability is remediated.
