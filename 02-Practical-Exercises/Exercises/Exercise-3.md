<img width="935" height="727" alt="Exercise 3" src="https://github.com/user-attachments/assets/f1434d09-8208-464d-9ce5-321ea369afcb" />

# MITRE ATT&CK Attack Sequence Analysis

## Task 1

### Initial Access

**09:02** — User opens email attachment **`Q3_Invoice.xlsm`**; the macro executes and spawns **`powershell.exe`**.

### Execution

**09:03** — **`powershell.exe`** downloads a file from a pastebin-style URL and saves it to **`%TEMP%`**.

### Persistence

**09:04** — A new **Registry Run Key** is created:

```text id="c6i5wq"
HKCU\...\Run\Updater = "%TEMP%\svchost_update.exe"
```

### Credential Access

**09:17** — **`svchost_update.exe`** reads browser credential store files, including **Chrome `Login Data`** and **Firefox `key4.db`**.

### C2 — Command and Control

**09:19** — An outbound connection is established to an external IP address over **port 443**, with a small recurring beacon sent every **60 seconds**.

## Task 2

At **09:41**, the attacker is displaying the **Discovery** tactic to identify other accounts in the domain with potentially greater access that could be exploited.

At **09:52**, the attacker is displaying **Lateral Movement** by connecting to **`fin-ws-11`** from **`fin-ws-07`** via **SMB**.

## Task 3

At **09:19**, the **beaconing** starts. By isolating the host from the network, we are terminating the attacker's **C2 (Command and Control)** communication.

> **Contain at the point that cuts off the attacker's remote control, not at the point where the damage was first done.**
