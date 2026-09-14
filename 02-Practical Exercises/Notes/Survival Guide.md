# Log reading, MITRE ATT&CK mapping, vulnerability prioritization, and incident response sequencing Guide

---

## 1. The Log-Reading Process (do this every time, in order)

When you're dropped into a log excerpt, don't start guessing at the "attack story." Work it mechanically first:

**Step 1 — Establish the baseline fields**
For every log line, identify: *who* (user/account), *what* (action/command), *from where* (source IP/host), *to what* (target host/resource), *when* (timestamp), *result* (success/fail).

**Step 2 — Look for repetition patterns**
- Many failures, same account, same source, tight time window → brute force
- Many failures, many accounts, same source → password spraying
- One failure then one success → possible successful brute force / credential stuffing
- Success from a source/time that doesn't match the user's normal pattern → possible compromised credential use

**Step 3 — Trace what happens *after* the interesting event**
The line that triggered the alert is rarely the whole story. Always read forward: what did the account/process do next? New accounts, new scheduled tasks, registry changes, outbound connections — these tell you the *intent*.

**Step 4 — Classify each action into a tactic (see Section 2) before you decide on a narrative**
Don't jump to "this is an APT" or "this is ransomware." Tag each line with a tactic first. The story falls out of the tags.

**Step 5 — Identify the pivot point**
Ask: which single line, if network/process access were cut off at that moment, would have stopped everything after it? That's usually your containment answer.

---

## 2. MITRE ATT&CK Tactic Cheat Sheet (artifact → tactic, memorize these pairings)

| Artifact you see in a log | Tactic |
|---|---|
| Phishing email/attachment opened, exploited public app | **Initial Access** |
| Macro runs, script/process spawned, command executed | **Execution** |
| Registry Run key, scheduled task/cron job, new service, new account created | **Persistence** |
| Exploiting a vuln to gain higher rights, bypassing UAC | **Privilege Escalation** |
| Disabling AV/logging, clearing event logs, obfuscated/encoded commands | **Defense Evasion** |
| Reading browser credential stores, dumping LSASS, keylogging | **Credential Access** |
| `net.exe`, `whoami /all`, enumerating groups/shares/AD | **Discovery** |
| SMB connections to other hosts using harvested creds, RDP pivoting | **Lateral Movement** |
| Staging files, archiving/zipping data before send-out | **Collection** |
| Beaconing to an external IP at regular intervals, DNS tunneling | **Command and Control (C2)** |
| Outbound transfer of staged/zipped data | **Exfiltration** |
| File encryption, service/data destruction, defacement | **Impact** |

**Common trap:** using an account's *existing* valid privileges (e.g., an admin account running sudo commands it's already entitled to) is **not** Privilege Escalation — that's Valid Accounts, usually tagged Persistence or Defense Evasion. Privilege Escalation means gaining rights you *didn't* have.

---

## 3. Vulnerability Prioritization Framework (don't rank by CVSS Base score alone)

Ask these questions in order — each one can override the previous:

1. **Is it in CISA's KEV (Known Exploited Vulnerabilities) catalog?** If yes, it jumps to the top regardless of score. KEV = confirmed active real-world exploitation, not just theoretical risk.
2. **Does a public exploit/PoC exist?** Exploit availability raises urgency even at a moderate CVSS score.
3. **What's the Attack Vector and Privileges Required?** Network + no auth needed = far more dangerous than Local + high complexity, even at similar scores.
4. **What's the asset's actual exposure?** A high CVSS score on an isolated, air-gapped, low-value asset gets *environmentally* downgraded — this is what the CVSS **Environmental metric group** exists for. Base score assumes worst-case generic exposure; environmental context adjusts it to your real network.
5. **What data/criticality is attached?** PII, financial data, or a critical public-facing asset raises priority even if the raw score is lower than another finding.

**Rule of thumb for PBQs:** CVSS Base score is a *starting point*, not the final verdict. The exam is testing whether you can override a high score with context (isolated network) and override a lower score with context (active exploitation, PII exposure).

---

## 4. Incident Response Phase Sequencing (NIST SP 800-61)

**Preparation → Detection & Analysis → Containment → Eradication → Recovery → Post-Incident (Lessons Learned)**

- **Detection & Analysis**: You've confirmed the alert is real and understand what happened. You are *not yet* taking action against the threat.
- **Containment**: Stop the bleeding — isolate the host, disable the compromised account, block the C2 IP. Goal: stop the attacker's *next* move, not undo what's already happened.
- **Eradication**: Remove the actual malicious artifacts — delete the backdoor account, remove the malware, close the vulnerability that got them in.
- **Recovery**: Bring systems back online, restore from clean backups, monitor closely for reinfection.
- **Lessons Learned**: Post-incident review, update playbooks/detections.

**Common trap:** jumping straight to Eradication (deleting the malicious account/malware) before Containment (cutting off network access). If you eradicate first, the attacker may still have an active session or C2 channel and can just recreate what you deleted.

**Containment timing rule:** isolate at the point that cuts off the attacker's *remote control* (e.g., when a C2 beacon starts), not the point where damage first occurred locally on disk — you can't undo local damage with network isolation, but you can stop it from being leveraged further.

---

## 5. Quick Pre-Answer Checklist (run this before submitting any PBQ answer)

- [ ] Did I tag every relevant log line with a tactic *before* building a narrative?
- [ ] Did I check for KEV/exploit availability instead of ranking by CVSS score alone?
- [ ] Did I consider environmental context (asset exposure, data sensitivity) before finalizing a vuln ranking?
- [ ] Did I sequence IR actions as Detection → Containment → Eradication → Recovery, not skipping a phase?
- [ ] Am I distinguishing "using existing valid privileges" from "actual privilege escalation"?
