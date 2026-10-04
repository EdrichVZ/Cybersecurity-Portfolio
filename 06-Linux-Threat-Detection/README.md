# Linux Threat Detection

A collection of Linux-focused security investigations covering **initial access, discovery, malware activity, privilege escalation, and persistence**.

The investigations focus on analysing Linux authentication logs, `auditd` telemetry, process relationships, and attacker behaviour to reconstruct malicious activity and understand how a Linux compromise progresses.

---

## Investigations

### [Linux Threat Detection 1 — Initial Access Investigation](./Linux-Threat-Detection-1)

Focuses on:

- SSH authentication analysis
- Brute-force detection
- Successful compromise
- Application exploitation
- Process-tree analysis
- Reverse-shell identification

---

### [Linux Threat Detection 2 — Discovery & Malware Investigation](./Linux-Threat-Detection-2)

Focuses on:

- System discovery
- Defence discovery
- Ingress tool transfer
- Malware deployment
- Cryptomining activity
- Internal network scanning

---

### [Linux Threat Detection 3 — Post-Exploitation & Persistence Investigation](./Linux-Threat-Detection-3)

Focuses on:

- Reverse shells
- Credential discovery
- Privilege escalation
- Systemd persistence
- Cron persistence
- Account persistence
- SSH-key persistence

---

## Key Skills

- Linux log analysis
- SSH investigation
- `auditd` and `ausearch`
- Process-tree reconstruction
- Command-line analysis
- Malware activity investigation
- Privilege-escalation detection
- Persistence detection
- Attacker TTP correlation
- Incident timeline reconstruction

---

## Attack Progression

```text
Initial Access
      ↓
Discovery
      ↓
Defence Discovery
      ↓
Tool Transfer
      ↓
Malware Execution
      ↓
Credential Discovery
      ↓
Privilege Escalation
      ↓
Persistence

---

##Conclusion

These investigations demonstrate how Linux telemetry can be used to move from isolated events to a broader understanding of attacker activity.
The focus throughout the project is on correlating authentication events, process activity, command execution, and persistence mechanisms to reconstruct a complete compromise.
