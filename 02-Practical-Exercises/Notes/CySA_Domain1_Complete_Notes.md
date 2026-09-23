# CompTIA CySA+ (CS0-003) — Domain 1.0: Security Operations
### Complete Study Notes — Chapters 1, 2, 3, 4 (33% of exam weight)

> **Note on scope:** Domain 1 is covered by **Chapters 1–4 only**. Chapter 5 ("Reconnaissance and Intelligence Gathering") is physically placed right after these chapters in the book, but its own objective-coverage box maps it to **Domain 2.0** (2.1/2.2), not Domain 1 — it belongs with your Domain 2 notes instead.

---

# Chapter 1 — Today's Cybersecurity Analyst
**Covers: Objective 1.5 (Efficiency and process improvement) + a small piece of 2.1 (Static vs. dynamic — reverse engineering)**

> ⚠️ Chapter 1 also spends significant time on general risk-assessment background (CIA triad, GAPP privacy principles, NIST SP 800-30's four threat categories, qualitative risk matrices). This is **not listed anywhere in the official CS0-003 objectives document** — it's foundational context the authors include, not tested material. Skim it for understanding, don't memorize it.

## 1.5 — Efficiency and Process Improvement (this IS on the official objectives)

### Standardize Processes
- Build a standardized process/playbook for any recurring task — reduces reinvention and ensures consistent team responses.

### Streamline Operations — Automation
- **SOAR (Security Orchestration, Automation, and Response)** — automates security tasks across multiple systems. "Minimizing human engagement" is CompTIA's phrase for this.
- Automation candidates share two traits: **repeatable** and **don't require human judgment**.
- SOAR also enriches threat intelligence — combining multiple threat feeds into one operational picture.

### Technology and Tool Integration
- **APIs** — programmatic interfaces letting you script actions a service's web UI could also do.
- **Webhooks** — one application sends a signal (web request) to trigger action in another (e.g., a new-vulnerability alert automatically triggers a rescan).
- **Plug-ins** — small add-on programs that extend another program's functionality (e.g., a browser plug-in auto-pulling WHOIS/reputation data on hover).

### Single Pane of Glass
- The goal: integrate all your tools into one consistent interface. Not fully achievable in practice ("there's always one more system"), but a useful **design principle** to reduce the number of interfaces analysts juggle daily.

## Bonus 2.1 content: Reverse Engineering
- **Reverse engineering** — working backward from a finished product to understand how it works (decomposition). Used on suspicious software or to verify hardware integrity.
- **Sandboxing / code detonation** — an automated reverse-engineering technique: isolate unknown code, execute it, and observe behavior (network scanning, data gathering, C2 communication) rather than relying on signatures.
- **Interpreted languages** (Python, Ruby) — reverse engineers can just read the source.
- **Compiled languages** (Java, C/C++) — much harder; requires a **decompiler** (often unreliable) or behavioral observation.
- **Fingerprinting/hashing software** — even without reverse-engineering compiled code, you can hash two files (e.g., SHA) and compare — identical hashes mean identical content.
- **Hardware reverse engineering** — even harder than software; relates to **source authenticity** (verifying hardware wasn't tampered with in the supply chain) — reference: NSA's documented interdiction of Cisco router shipments (Snowden leaks).

---

# Chapter 2 — System and Network Architecture
**Covers: Objective 1.1 in full (Log ingestion, OS concepts, Infrastructure concepts, Network architecture, IAM, Encryption, Sensitive data protection)**

## Infrastructure Concepts
| Concept | Key idea |
|---|---|
| **Serverless (FaaS)** | Code runs only when called (AWS Lambda, Google App Engine, Azure Functions); security controls resemble software dev + cloud access controls |
| **Virtualization** | Multiple OSes run on shared physical hardware; enables VDI, virtualization host clusters, virtual security appliances |
| **Containerization** | Application-level virtualization (Docker, Kubernetes); lightweight, portable; shares the host OS, so a host-level compromise can affect many containers |

## OS Concepts
- **System hardening** — reduce attack surface: patch, remove unneeded software/services, restrict/log admin access, control account creation, enable logging, use disk encryption/secure boot. Often based on **CIS benchmarks**.
- **Windows Registry** — database of OS settings; 5 root keys: **HKCR, HKLM, HKU, HKCU, HKCC**. High-value persistence target for attackers.
- **File structure / configuration file locations** (explicitly named on objectives — macOS is *not* emphasized on the exam):
  - Windows: Registry, plus `C:\ProgramData\`, `C:\Program Files\`, user `AppData`
  - Linux: `/etc/`
- **System processes** — you don't need every Windows process memorized; know that attackers disguise malicious processes with names resembling legitimate ones (e.g., near-matches to `svchost.exe`), and target processes for privileged access.
- **Hardware architecture** — x86 (Intel/AMD) is still dominant, but ARM-based chips (Apple M1/M2) don't natively run x86 malware — relevant to what malware can/can't execute on a given device.

## Log Ingestion (exam explicitly lists only these two sub-topics)
- **Time synchronization** — via **NTP**; critical because unsynchronized clocks make cross-system log correlation unreliable.
- **Logging levels** — Cisco-style 0–7 scale (0=Emergencies … 7=Debugging). Too low a level misses data; too high (level 7) floods you with noise.

## Network Architecture
- **On-premises** — firewalls, IDS (alerts only) vs. IPS (blocks), content filtering, NAC, network scanners, UTM (combines multiple functions).
- **Cloud** — SaaS/PaaS security is largely contractual (vendor-controlled); IaaS gives you more direct OS/config responsibility. VPC = isolated private-subnet cloud environment.
- **Hybrid** — combines on-prem + cloud; added complexity since each environment needs its own security model.
- **Network segmentation** — reduces attack surface, limits compliance scope, can improve availability, improves efficiency. Achieved via firewalls, VLANs, or physical separation (**air gap**). ⚠️ Air gaps aren't foolproof — Stuxnet crossed an air gap via an infected USB drive.
  - **Jump box/jump server** — a controlled access point spanning two security zones; must be tightly secured/monitored.
- **Zero trust** — no implicit trust for anything inside the perimeter; every action is verified before being allowed.
- **SASE** (exam calls it "Secure Access **Secure** Edge," industry usually says "Secure Access **Service** Edge" — know both phrasings) — combines SD-WAN + security functions (CASB, zero trust, FWaaS, antimalware) at the edge.
- **SDN (Software-Defined Networking)** — centralized, API-driven network control (e.g., OpenFlow) across multi-vendor hardware.

## Identity and Access Management (IAM)
- **AAA** = Authentication, Authorization, Accounting.
- **MFA** — 2+ distinct factor *types*: knowledge (password), possession (token/authenticator app), biometric (fingerprint), and (less common) location/GPS.
- **Passwordless** — typically a single, stronger factor (USB token, authenticator app) instead of a password.
- **SSO** — one login, multiple systems. Common tech: LDAP, CAS. Shared authentication (OpenID, OAuth, OpenID Connect, Facebook Connect) is related but distinct — shared auth reuses an identity across sites but doesn't necessarily give a true single-session SSO experience.
- **Federation** — links identity across separate organizations/systems (e.g., "Login with Google"). Key technologies: **SAML, AD FS, OAuth, OpenID Connect**. Security depends on the weakest federation member.
- **PAM (Privileged Access Management)** — manages/secures privileged accounts (not just root/admin — also service accounts, "break glass" emergency accounts) based on least privilege; addresses over-provisioning and privilege creep.
- **CASB (Cloud Access Security Broker)** — policy enforcement point (on-prem or cloud) for cloud service usage; helps with data security, antimalware, visibility, risk management.

## Encryption
- **PKI** — issues certificates for encryption, authentication, code signing. 5 components: **CA** (issues/signs certs), **RA** (verifies requester identity), directory (stores keys), certificate management system, certificate policy. **CRL** allows revoking a cert before expiration.
- **SSL Inspection** (really TLS inspection, but the term "SSL" persists) — intercepts/terminates encrypted connections at an inspection device so tools like IPS/DLP can inspect otherwise-encrypted traffic, then re-encrypts to the real destination. Requires devices to trust the inspection device's certificate. Creates a potential single point of compromise if the inspection system itself is breached.

## Sensitive Data Protection
- **DLP** — protects data in motion, at rest, and in use; combines endpoint software + network visibility.
- **PII** — any info that could reasonably identify an individual (financial/medical records, SSNs, addresses).
- **CHD (Cardholder Data)** — PAN, cardholder name, expiration date; "sensitive authentication data" adds CVV, magstripe/chip data, PIN. Often called "PCI data."
> Note: PHI (Protected Health Information) is mentioned by the book as a related concept but is explicitly **not** part of the official exam objectives.

---

# Chapter 3 — Malicious Activity
**Covers: Objectives 1.2 (indicators of malicious activity) and 1.3 (tools/techniques to determine malicious activity) in full**

## Network-Related Indicators (1.2)
- **Bandwidth consumption** — can signal service disruption or a symptom of exfiltration.
- **Beaconing** — periodic C2 "check-in" traffic (HTTP/HTTPS), often encrypted and blended with normal traffic — hard to detect; look for regular timing intervals.
- **Irregular peer-to-peer communication** — systems talking to each other when they shouldn't (e.g., inside a datacenter where only outbound-only traffic is expected).
- **Rogue devices** — unauthorized devices on the network. Detection: MAC vendor lookups, site surveys, wireless rogue detection (including spoofed legitimate SSIDs).
- **Scans/sweeps** — sequential port testing, many-host connection attempts; usually a precursor to a focused attack, not damaging by itself.
- **Unusual traffic spikes** — detected via baselines (anomaly-based), heuristics (behavior-based/rule-based), or protocol analysis.
- **Activity on unexpected ports** — either scanning/probing, or attacker-run services on non-standard ports.

### Monitoring Methods (background for the above)
- **Router-based monitoring** — NetFlow/sFlow/J-Flow (traffic flow data, often sampled) + SNMP (device status).
- **Active monitoring** — reaches out to systems directly (e.g., ping, iPerf) — adds network load.
- **Passive monitoring** — captures traffic via taps without generating traffic itself.

## Host-Related Indicators (1.2)
- **Processor / memory / drive capacity consumption** — sudden unexplained spikes worth investigating.
- **Unauthorized software / malicious processes** — via allowlisting, EDR, antivirus, manual review.
- **Abnormal OS process behavior** — process injection (e.g., via Metasploit) or naming malicious processes to resemble legitimate ones.
- **Unauthorized changes / privileges** — tracked via central management suites, SIEM, file/directory integrity checking, security event logs.
- **Data exfiltration** — tools: EDR, IPS, DLP.
- **File system changes/anomalies** — real-time monitoring + checksum comparison against known-good baselines.
- **Registry changes/anomalies** — attackers favor Registry `Run`/`RunOnce` keys for persistence; monitor via registry monitoring tools or lockdown policies.
- **Unauthorized scheduled tasks** — Windows Task Scheduler / Linux cron jobs are common persistence mechanisms; review via Task Scheduler GUI or `schtasks` from the command line.

## Application-Related Indicators (1.2)
- **Anomalous activity / unexpected output / service interruption** — via application logs and behavior monitoring.
- **Introduction of new accounts** — attackers create accounts as part of persistence; watch app-level and OS-level account creation, especially in high-signup-volume environments where it's easy to hide.
- **Unexpected outbound communication** — an application reaching out somewhere it normally wouldn't.
- **Application logs** — Windows Application log; Linux `/var/log`.

## Other (1.2's catch-all category — still tested!)
- **Social engineering** — detection relies on awareness training, non-punitive reporting culture, and impact analysis after an attempt.
- **Obfuscated links** — intentionally deceptive URLs used to trick users into clicking malicious links, common in phishing.

---

## Tools and Techniques to Determine Malicious Activity (1.3)

### Tools (officially named)
| Tool | Purpose |
|---|---|
| **Wireshark** | GUI packet capture/inspection (cross-platform) |
| **tcpdump** | CLI packet capture (Linux-native) |
| **SIEM** | Centralized log correlation across the org |
| **SOAR** | Cross-tool automation + incident response orchestration |
| **EDR** | Endpoint-focused detection using IOCs + behavioral analysis (largely supersedes traditional signature-based AV) |
| **WHOIS** | Domain/IP registration lookup for reputation checks |
| **AbuseIPDB** | Public IP/domain/network reputation lookup |
| **Strings** | Extracts readable text from binary files for analysis |
| **VirusTotal** | Multi-engine file/URL scanning and reputation service |
| **Joe Sandbox** | Automated malware behavioral analysis (network calls, API calls, etc.) |
| **Cuckoo Sandbox** | Open-source automated malware analysis |

### Common Techniques
- **Pattern recognition (esp. Command-and-Control detection)** — traffic to known-bad IPs, unexpected ports/protocols, large data transfers, traffic from processes that shouldn't generate network traffic (e.g., `notepad.exe`), off-hours traffic timing.
- **Interpreting suspicious commands** — recognizing malicious command-line activity in logs/history.
- **Email analysis**:
  - **Header analysis** — check SPF/DKIM/DMARC results in headers, `Received-From`, Reply-To anomalies. ⚠️ Forwarded emails lose original headers (breaks SPF validation at the new destination).
  - **Impersonation** — spoofed sender identity.
  - **DKIM** — cryptographically signs outgoing mail; verified against a public key in DNS.
  - **SPF** — publishes a list of servers authorized to send mail for a domain.
  - **DMARC** — combines SPF + DKIM to decide what to do (reject/quarantine) with mail that fails checks.
  - **Embedded links** — a common phishing vector; check what they actually point to, not just the display text.
- **File analysis**:
  - **Hashing** — compare hash values to detect identical or modified files.
  - (Strings analysis, covered above under tools)
- **User behavior analysis**:
  - **Abnormal account activity** — off-hours logins, privilege use inconsistent with role, dormant-account activity.
  - **Impossible travel** — logins from geographically distant locations in a timeframe that rules out physical travel (e.g., US login, then Japan login 15 minutes later). Related tool category: **UEBA** (User and Entity Behavior Analytics).

### Programming Languages/Scripting (officially named — know general purpose, not deep syntax)
- **JSON** — human-readable key-value data interchange (look for curly braces `{}`).
- **XML** — markup-based data interchange.
- **Python** — popular general-purpose scripting language for security automation.
- **PowerShell** — Windows-native scripting (note: execution policy like `Set-ExecutionPolicy RemoteSigned` often must be configured before scripts run).
- **Shell script** — Linux/Unix command-line automation (Bash, etc.).
- **Regular expressions** — pattern matching, commonly paired with `grep` for searching log/text data. Pipes (`|`) chain command output between tools.

---

# Chapter 4 — Threat Intelligence
**Covers: Objective 1.4 in full**

## Threat Data and Intelligence Sources
- **Open source intelligence (OSINT)** — publicly available: social media, blogs/forums, government bulletins, CERT/CSIRT advisories, deep/dark web.
- **Closed/proprietary source intelligence** — paid feeds, information-sharing organizations, internal sources — often part of a commercial vendor service.

## Assessing Threat Intelligence (officially named 3 criteria)
- **Timeliness** — delayed feeds risk missing or reacting too late to a threat.
- **Accuracy** — reliability of the underlying source(s); single-source vs. multi-source corroboration.
- **Relevancy** — even timely, accurate data is useless if it doesn't apply to your platform/environment.
- **Confidence levels/scores** — feeds often rate findings (e.g., Confirmed/Probable/Possible/Doubtful/Improbable/Discredited, or simple High/Medium/Low or 1–10 scales) — low confidence doesn't mean ignore, just weight accordingly.

## Threat Intelligence Sharing (5 officially named use cases)
1. **Incident response** — identifying actors, TTPs, speeding target identification/response
2. **Vulnerability management** — informs patch prioritization/urgency
3. **Risk management** — overall organizational risk posture
4. **Security engineering** — informs future design decisions
5. **Detection and monitoring** — faster rule creation, better behavioral detection

> ⚠️ **Not on the official objectives** (the book explicitly says so itself): **STIX** (Structured Threat Information Expression, XML-based threat data standard), **TAXII** (its companion transport protocol), and **OpenIOC**. Good real-world context, but the book states directly you "shouldn't run into a question directly about them on the exam."

## Threat Actors (officially named list — memorize)
| Actor | Key trait |
|---|---|
| **Nation-state** | Most resources; often behind APTs |
| **Organized crime** | Financially motivated (e.g., ransomware) |
| **Hacktivists** | Politically/philosophically motivated (e.g., Anonymous) |
| **Script kiddies** | Use existing tools unsophisticatedly — still dangerous |
| **Insider threat** | Intentional OR unintentional — exam explicitly splits these two |
| **Supply chain** | May insert malicious hardware/software into the supply chain, or attack/disrupt it directly |

## TTP (Tactics, Techniques, and Procedures)
- Framework for classifying and countering APT behavior: how they initiate (recon, probes, social engineering), what infrastructure/techniques they use, and their compromise/cleanup patterns.

## Threat Hunting (officially named focus areas)
- **Configurations/misconfigurations** — settings that enable or reveal compromise.
- **Isolated networks** — easier to baseline, but harder to reach with centralized tooling.
- **Business-critical assets and processes** — prioritized due to organizational risk.

### IOC — 3 Lenses (officially named)
1. **Collection** — gathering data via tools/logs that might indicate compromise.
2. **Analysis** — determining whether gathered data actually indicates compromise (context matters — "unusual traffic" could be benign).
3. **Application** — using confirmed IOCs to trigger incident response, and feeding IOCs into shared threat intel for others.

### Active Defense and Honeypots (officially named)
- **Active defense** (primary CySA+ meaning) — deception techniques that delay/confuse attackers (e.g., tarpits with fake targets). *(A more aggressive "hack-back" definition exists but is unlikely to be tested due to legal/liability issues.)*
- **Honeypot** — an intentionally vulnerable, instrumented system used to lure and observe attacker behavior.
- (Related but not explicitly named on objectives: honeynets = networks of honeypots; darknets = monitored unused IP ranges.)

---

## Exam Essentials Checklist — Domain 1
- [ ] Know SOAR, APIs, webhooks, plug-ins, and "single pane of glass" (1.5)
- [ ] Know serverless/virtualization/containerization trade-offs (1.1)
- [ ] Know Windows Registry root keys, config file locations (Windows vs. Linux), and why hardware architecture matters (1.1)
- [ ] Know NTP's role and Cisco's 0–7 logging levels (1.1)
- [ ] Know on-prem/cloud/hybrid, segmentation, zero trust, SASE, SDN (1.1)
- [ ] Know MFA factor types, SSO vs. federation vs. shared auth, PAM, CASB (1.1)
- [ ] Know PKI's 5 components, SSL/TLS inspection tradeoffs (1.1)
- [ ] Know DLP, PII, CHD definitions (1.1)
- [ ] Know all network/host/application indicator categories verbatim (1.2)
- [ ] Know the full official tool list: Wireshark, tcpdump, SIEM, SOAR, EDR, WHOIS, AbuseIPDB, Strings, VirusTotal, Joe Sandbox, Cuckoo Sandbox (1.3)
- [ ] Know DKIM/SPF/DMARC roles and why forwarding breaks SPF (1.3)
- [ ] Know impossible travel as the classic user-behavior IOC (1.3)
- [ ] Know JSON/XML/Python/PowerShell/Shell/Regex at a conceptual level (1.3)
- [ ] Know timeliness/accuracy/relevancy as the 3 confidence-level factors (1.4)
- [ ] Know the 5 threat-intel-sharing use cases (1.4)
- [ ] Know all 6 named threat actor types, including insider (intentional/unintentional) (1.4)
- [ ] Know the 3 threat-hunting focus areas and the 3 IOC lenses (1.4)
- [ ] Know active defense and honeypots (1.4)
- [ ] STIX/TAXII/OpenIOC, GAPP, and the NIST 800-30 risk framework are background — deprioritize if short on time
