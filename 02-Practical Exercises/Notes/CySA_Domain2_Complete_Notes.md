# CompTIA CySA+ (CS0-003) — Domain 2.0: Vulnerability Management
### Combined Study Notes — Chapters 5, 6, 7, 8 (30% of exam weight)

> **Correction note:** Chapter 5 ("Reconnaissance and Intelligence Gathering") was originally left out of this document because the book places it physically before Chapter 6 rather than grouped with it — but its own objective-coverage box maps it to **Domain 2.0, objectives 2.1 and 2.2**. It's included below and is where the previously-missing tools (Nmap, Metasploit, Angry IP Scanner, Maltego, Recon-ng) and asset-discovery sub-topics (map scans, device fingerprinting) actually come from.

---

# Chapter 5 — Reconnaissance and Intelligence Gathering
**Domain 2.0: Vulnerability Management | Objectives 2.1, 2.2**

## Asset Discovery — Map Scans and Device Fingerprinting (2.1 named sub-bullets)
- **Map scans** — active reconnaissance techniques (ping sweeps, traceroute-style hop analysis, port scans) used to build a picture of network topology: what hosts exist, how they're connected, and where security devices sit. Results can be imperfect — firewalls, load balancers, and cloud/virtualized environments can distort or hide the real topology.
- **Device fingerprinting** — identifying an OS/device type by analyzing how it responds to network probes (TCP/IP stack quirks, TTL values, response timing, banner information). Different OS versions respond subtly differently, which tools use to guess the target's identity.
- **Passive discovery** — gathering information without directly probing hosts (e.g., analyzing existing traffic captures, DNS records, WHOIS data) — stealthier but less thorough than active scanning.

## Getting Permission First
- Scanning without authorization can be illegal and can disrupt fragile services or flood logs — always confirm scope/permission before scanning (ties to the "special considerations" bullet in 2.1: scheduling, operations, performance, sensitivity levels).

## Common Reconnaissance/Scanning Tools (2.2 — the previously-missing official tools)
| Tool | Category (per objectives) | Notes |
|---|---|---|
| **Nmap** | Multipurpose | The most widely used CLI port scanner; does host discovery, port scanning, service/version identification, and OS fingerprinting. **Zenmap** is its official GUI, which includes a built-in topology mapper. |
| **Angry IP Scanner** | Network scanning and mapping | Cross-platform (Windows/Linux/macOS) GUI port scanner; simpler and less detailed than Nmap (no service/OS identification by default) — needs Java. |
| **Maltego** | Network scanning and mapping | OSINT/link-analysis tool with a GUI; uses "transforms" (server-side actions) to pull and visually correlate data between entities (domains, people, infrastructure). |
| **Metasploit Framework (MSF)** | Multipurpose | The leading penetration testing framework; goes beyond scanning into actual exploitation. Includes scanning modules (`tcp`, `syn`) and a web app vulnerability scanning module (`wmap`). |
| **Recon-ng** | Multipurpose | Modular CLI reconnaissance framework (built into Kali Linux); uses a "marketplace" of installable modules; integrates with OSINT sources like Shodan and hackertarget. |

## Log and Configuration Analysis (supporting content for 2.1/2.2 scenario questions)
- **Network device logs** — often go to console ports by default; SNMP can centralize collection. Device *configuration files* often reveal more than logs (routing info, interfaces, ACLs).
- **NetFlow** — Cisco protocol for collecting IP traffic flow information (other vendors have their own equivalents).
- **Netstat** — local host tool showing active connections and routing tables (`-nr` flag shows the routing table).
- **DHCP logs/config** — reveal lease assignments and can help correlate an IP back to a specific device/time.
- **Firewall logs/configs** — reveal both allowed/blocked traffic and can let a tester reverse-engineer the rule set itself.
- **DNS and WHOIS** — `nslookup`/`dig` for record types (A, MX, NS, etc.); WHOIS reveals domain registration info; **zone transfers** (if misconfigured/allowed) can leak an organization's entire internal DNS structure — a classic reconnaissance/misconfiguration finding.

---

# Chapter 6 — Designing a Vulnerability Management Program
**Domain 2.0: Vulnerability Management | Objectives 2.1, 2.2**

---

## 1. Identifying Vulnerability Management Requirements

### Regulatory Environment
Most laws (HIPAA, GLBA) don't explicitly require vulnerability scanning — they're about *data handling*, not scan mandates. Two frameworks **do** explicitly require it:

| Standard | Who it applies to | Key scan requirements |
|---|---|---|
| **PCI DSS** | Merchants/service providers handling credit card data. NOT a law — it's a contractual requirement enforced by the PCI Security Standards Council (funded by industry). | • Both internal AND external scans required<br>• Minimum **quarterly** + after any significant change<br>• Internal scans → qualified internal personnel<br>• External scans → must be done by an **Approved Scanning Vendor (ASV)**<br>• Must remediate high-risk vulns and rescan until "clean" |

> ⚠️ **Not on the official CySA+ objectives:** the textbook also covers **FISMA** (U.S. federal agencies, ties to **FIPS 199** and **NIST SP 800-53**) as a second regulatory example. The official CompTIA objectives document only names **PCI DSS, CIS benchmarks, OWASP, and the ISO 27000 series** under "Industry frameworks" (2.1) — FISMA/FIPS 199 aren't listed. Good context, but don't spend heavy study time memorizing FISMA specifics for this exam.

⚠️ **Never scan without explicit authorization** — it can violate policy or law.

### Corporate Policy
Even without regulation, most orgs mandate scanning via internal policy because it's accepted best practice.

### Industry Standards (memorize differences — exam favorite)
- **CIS (Center for Internet Security)** — publishes **benchmarks**: consensus-based, detailed *configuration hardening* guides for OSes/apps/devices.
- **ISO 27001** — standard for setting up an **Information Security Management System (ISMS)** (organizations can get *certified* against this).
- **ISO 27002** — companion standard with detailed **control specifics**.
- **OWASP** — home of the **OWASP Top 10** web app vulnerabilities + secure coding guidance + free tools (ZAP).

**OWASP Top 10 (2021):**
1. Broken access control
2. Cryptographic failures
3. Injection
4. Insecure design
5. Security misconfiguration
6. Vulnerable and outdated components
7. Identification and authentication failures
8. Software and data integrity failures
9. Security logging and monitoring failures
10. Server-side request forgery (SSRF)

### Identifying Scan Targets
Decide scope using: data classification, internet exposure, services offered, prod/test/dev status. Use automated **asset discovery/inventory** scans to find known *and unknown* devices first. *(See the Chapter 5 section above for map scans and device fingerprinting — the named Asset Discovery sub-bullets.)*

### Security Baseline Scanning (2.1)
Comparing a system's actual configuration against an approved, hardened baseline (e.g., a CIS benchmark) to catch configuration drift — distinct from a vulnerability scan, which looks for known flaws rather than deviation from a standard.

### Scheduling Scans
Frequency depends on:
- **Risk appetite** — org's willingness to tolerate risk
- Regulatory/corporate policy minimums
- **Performance constraints** (scanner throughput)
- **Operational constraints** (avoid business-critical windows)
- **Licensing limitations**

### Active vs. Passive Scanning
| | Active | Passive |
|---|---|---|
| Method | Directly probes/connects to hosts | Monitors network traffic (like an IDS) |
| Pros | High-quality, thorough results | Stealthy, no risk of disruption |
| Cons | Noisy, detectable, can disrupt prod systems, can miss segmented/firewalled hosts | Only sees what's reflected in traffic; not a full replacement |
| Relationship | Primary method | **Complements** active scanning |

---

## 2. Configuring and Executing Vulnerability Scans

### Scoping
Answer: What systems/networks are in scope? How will presence be tested? What tests run?
- **Scoping for compliance** — network segmentation can shrink PCI DSS scope to just the cardholder-data systems, cutting cost/effort dramatically.

### Scan Sensitivity & Plug-ins
- Scans typically start from a **template**, then get customized.
- **Plug-ins** = individual vulnerability checks, grouped by OS/app family. Disable irrelevant plug-ins → faster scans, fewer **false positives**.
- **False positive** = scanner flags normal activity as a threat.
- **False negative** = scanner *misses* a real issue.
- Dangerous/disruptive plug-ins should be tested in a **sandbox/test environment** first.

### Supplementing Network Scans
- **Credentialed scan** — scanner logs in with a (read-only, least-privilege) account to inspect real config (e.g., confirm a patch is actually installed). More accurate than non-credentialed.
- **Non-credentialed scan** — remote-only, more false positives, but no access needed.
- **Agent-based scanning** — small software agent installed on the host reports "inside-out." Roll out conservatively (pilot first) due to performance/stability concerns.
- **Agentless** — traditional network-based, no software installed on target.

### Scan Perspective
- **External scan** — run from the Internet; attacker's outside view. (PCI DSS requires this via an ASV.)
- **Internal scan** — from inside the corporate network; malicious-insider view.
- **Datacenter/agent-based** — most accurate; shows what firewalls/IDS/IPS/segmentation might otherwise hide.

### Scanner Maintenance
- Keep **scanner software** patched (scanners themselves have CVEs!).
- Keep **vulnerability plug-in feeds** updated — ideally daily, mostly automated but spot-check manually.

### SCAP — Security Content Automation Protocol (NIST-led standard)
> ⚠️ **Not on the official CySA+ objectives** — this is textbook-only depth. CVE and CVSS individually are worth knowing (they show up elsewhere), but SCAP as a named framework, and CCE/CPE/XCCDF/OVAL specifically, are not listed in CompTIA's objectives document. Low priority for study time.

| Component | Purpose |
|---|---|
| **CCE** – Common Configuration Enumeration | Standard names for config issues |
| **CPE** – Common Platform Enumeration | Standard names for product/version |
| **CVE** – Common Vulnerabilities and Exposures | Standard names for software flaws |
| **CVSS** – Common Vulnerability Scoring System | Standardized severity scoring |
| **XCCDF** – Extensible Configuration Checklist Description Format | Language for checklists & results |
| **OVAL** – Open Vulnerability and Assessment Language | Language for low-level test procedures |

---

## 3. Developing a Remediation Workflow

**Vulnerability management life cycle:** Detection → Remediation → Testing (cyclical)

### Reporting and Communication
- **Management dashboards** — high-level health summary.
- **Technical summary reports** — sortable by type/severity/host group.
- **Per-system reports** — checklist for system engineers.
- **Per-vulnerability detail reports** — root cause + remediation steps.

### Prioritizing Remediation (4 key factors — no fixed formula, it's judgment-based)
1. **Criticality** of systems/data affected (CIA impact)
2. **Difficulty** of remediation (cost/effort vs. benefit)
3. **Severity** (often via CVSS score)
4. **Exposure** (internet-facing issues generally outrank internal-only issues of similar severity)

### Testing & Implementing Fixes
- Test fixes in a sandbox first.
- After deploying, **re-scan to verify** remediation worked.
- Update your **configuration baseline** so future systems are built already-patched.

### Delayed Remediation Options (when you can't fix immediately)
1. **Compensating control** — e.g., a WAF blocking SQLi while the underlying app code stays vulnerable.
2. **Risk acceptance** — formally acknowledge and accept the risk.

---

## 4. Overcoming Risks / Objections to Vulnerability Scanning
- **Service degradation** — most common objection; mitigate by tuning scan intensity and scheduling around business hours.
- **Customer commitments (MOUs/SLAs)** — get scanning language written into agreements up front; give customers advance notice.
- **IT governance / change management** — work within these processes rather than around them.

---

## 5. Vulnerability Assessment Tools *(Objective 2.2)*

> Network scanning/mapping tools (Angry IP Scanner, Maltego) and multipurpose tools (Nmap, Metasploit, Recon-ng) are covered in the **Chapter 5 section above** — they're grouped there since that's where the textbook actually introduces them. Below are the remaining tool categories from Chapter 6.

### Vulnerability Scanners (officially named in objectives)
- **Nessus** (Tenable) — the classic, oldest major player.
- **OpenVAS** — free/open-source alternative.
> ⚠️ The textbook also mentions **Qualys** and **Nexpose** (Rapid7) as comparable commercial scanners — useful context, but they are **not named in the official CySA+ objectives** (only Nessus and OpenVAS are listed under "Vulnerability scanners").

### Cloud Infrastructure Assessment Tools *(exam requires these three open-source tools)*
| Tool | What it does |
|---|---|
| **Scout Suite** | Multicloud auditing (AWS, Azure, GCP, Alibaba, Oracle) — pulls config via APIs, flags issues (e.g., unencrypted EBS volumes) |
| **Pacu** | NOT a scanner — an **AWS exploitation framework** (like Metasploit for AWS); tests what an attacker could *do* with existing access |
| **Prowler** | Security configuration testing for AWS, Azure, GCP — similar purpose to Scout Suite, deeper on some checks |

### Web Application Scanners
Test for SQLi, XSS, CSRF, etc. via malicious input + fuzzing.
- **Nikto** — open source, CLI, exam-required.
- **Arachni** — open source, GUI-based, exam-required, cross-platform.
- Commercial network scanners (Nessus, Qualys, Nexpose) also have web-scanning modules.

### Interception Proxies *(classified as exploit tools — sit between browser and server)*
- **ZAP (Zed Attack Proxy)** — free, OWASP-run.
- **Burp Suite / Burp Proxy** — PortSwigger; free "Community" edition available, full suite is paid.

---

## Exam Essentials Checklist
- [ ] Know PCI DSS requirements (quarterly scans, ASV for external) — FISMA is bonus context, not on official objectives
- [ ] Know CIS vs ISO 27001 vs ISO 27002 vs OWASP purposes
- [ ] Know active vs. passive scanning trade-offs
- [ ] Know credentialed vs. non-credentialed, agent vs. agentless, internal vs. external
- [ ] Know the 4 remediation prioritization factors
- [ ] Know compensating control vs. risk acceptance as delayed-remediation options
- [ ] Know map scans and device fingerprinting as named Asset Discovery sub-bullets (Ch. 5)
- [ ] Know the full official 2.2 tool list: **Nessus, OpenVAS** (vuln scanners); **Angry IP Scanner, Maltego** (network scanning/mapping); **Nmap, MSF, Recon-ng** (multipurpose); **Scout Suite, Pacu, Prowler** (cloud); **Nikto, Arachni, Burp Suite, ZAP** (web app scanners); **Immunity Debugger, GDB** (debuggers, covered in Ch. 8 notes)
- [ ] SCAP/CCE/CPE/XCCDF/OVAL and Qualys/Nexpose are textbook extras, not official objectives — deprioritize if short on time
-e 

---


# Chapter 7 — Analyzing Vulnerability Scans
**Domain 2.0: Vulnerability Management | Objectives 2.1 (Critical Infrastructure), 2.3, 2.4**

---

## 1. Anatomy of a Vulnerability Scan Report
A typical scanner report (e.g., Nessus) includes, section by section:
1. **Name + Severity** (Low/Medium/High/Critical)
2. **Description** of the flaw
3. **Solution** — how to fix it
4. **See Also** — external references (blogs, docs, IETF, etc.)
5. **Output** — verbatim data returned by the probe (great for spotting false positives)
6. **Port/Hosts** — exactly where the vulnerability lives
7. **Vulnerability Information** — misc context (e.g., news coverage)
8. **Risk Information** — CVSS score + vector
9. **Plug-in details** — ID, publish/update dates

---

## 2. CVSS (Common Vulnerability Scoring System)
Industry-standard **0–10 severity scale**. 8 metrics total: 4 **exploitability** metrics, 3 **impact** metrics, 1 **scope** metric.

### Exploitability Metrics
| Metric | Values (low→high risk) |
|---|---|
| **Attack Vector (AV)** | Physical (0.20) → Local (0.55) → Adjacent Network (0.62) → **Network (0.85)** |
| **Attack Complexity (AC)** | High (0.44) → **Low (0.77)** |
| **Privileges Required (PR)** | High (0.27/0.50) → Low (0.62/0.68) → **None (0.85)** *(second value used if Scope=Changed)* |
| **User Interaction (UI)** | Required (0.62) → **None (0.85)** |

### Impact Metrics (Confidentiality / Integrity / Availability — same scale for each)
| Value | Description | Score |
|---|---|---|
| None (N) | No impact | 0.00 |
| Low (L) | Partial/limited impact | 0.22 |
| High (H) | Total compromise | 0.56 (Availability High = 0.56, "system completely shut down") |

### Scope (S) Metric
- **Unchanged (U)** — impact stays within the same security authority.
- **Changed (C)** — the exploit can affect resources *beyond* the vulnerable component's own security authority (affects the PR score and the impact formula).

### Reading a CVSS Vector
`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`
→ Version 3.1, Network vector, Low complexity, No privileges required, No user interaction, Scope Unchanged, High confidentiality impact, No integrity/availability impact.

### Calculating the Base Score (know the *process*, not memorization of math)
1. **Impact Sub-Score (ISS)** = 1 − [(1−Confidentiality)×(1−Integrity)×(1−Availability)]
2. **Impact Score** = ISS × 6.42 (if Scope=Unchanged) — more complex formula if Scope=Changed
3. **Exploitability Score** = 8.22 × AV × AC × PR × UI
4. **Base Score**:
   - If Impact = 0 → Base score = 0
   - If Scope Unchanged → Base = Impact + Exploitability
   - If Scope Changed → Base = (Impact + Exploitability) × 1.08
   - Cap at 10.
- NIST provides an online **CVSS calculator** — you won't hand-calculate this on the exam, but understand what each metric *means*.

### CVSS Qualitative Rating Scale
| Score | Rating |
|---|---|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

**Exploitability / Weaponization** = how likely/easy it is for an attacker to build a working exploit for the vuln.

---

## 3. Validating Scan Results

### The Four Outcomes (memorize this 2×2)
| | Vulnerability actually exists | Vulnerability does NOT exist |
|---|---|---|
| **Scanner reports it (positive)** | **True Positive** | **False Positive** |
| **Scanner doesn't report it (negative)** | **False Negative** (dangerous — missed!) | **True Negative** |

- Analysts should manually verify results, drawing on the expertise of DBAs, sysadmins, developers, etc.

### Documented Exceptions
- Organizations may formally accept not fixing something (e.g., unsupported OS needed for business reasons). Document the exception in the vuln management system so the scanner stops re-flagging it every run.
- ⚠️ Exceptions can conflict with **compliance obligations** — be careful.

### Informational Results
- Many scan findings are just "Info" category — not scored/severity-rated, just recon-style data. Prioritize High → Medium first; informational items often get a documented policy (e.g., "log decision after 2-3 consecutive appearances") rather than active remediation.

### Reconciling with Other Data Sources
Cross-reference scan results against:
- **Logs** (server/app/network)
- **SIEM** correlated data
- **Configuration management systems**

### Trend Analysis
Track new vulnerabilities over time, age of open vulnerabilities, and remediation time — reveals whether the program is improving.

### Context Awareness
- Internet-facing systems > internal-only systems, all else equal.
- **Asset value** matters — higher-value assets get higher remediation priority.

### Zero-Day Vulnerabilities
- Unknown to the vendor, so **no patch exists**. Extremely dangerous, favored by APTs.
- Example: **Stuxnet** — attributed to US/Israel, hit Iranian uranium enrichment ICS/SCADA systems.

---

## 4. Common Vulnerabilities (Objective 2.4)

### Server & Endpoint
- **Missing patches** — most common finding; scan reports typically link the vendor bulletin/patch.
- **Mobile devices** — often invisible to network scans (not always connected); manage via **MDM** (patch enforcement, encryption, remote wipe, app allow-listing).
- **End-of-life/outdated components (EOL)** — vendor stops releasing patches. Best practice if you must keep running it: isolate the system, no network connection if possible, add compensating controls (monitoring, strict firewall rules).
- **Buffer overflow** — attacker overflows allocated memory to overwrite adjacent memory with executable instructions.
  - **Stack overflow** — targets the stack (OS-managed).
  - **Heap overflow** — targets the heap (developer-managed).
  - **Integer overflow** — a buffer overflow variant where an arithmetic result is too large for its buffer.
- **Privilege escalation** — turning a normal account into an admin/root account (e.g., Linux "Dirty COW"). **Rootkits** automate this.
- **Remote code execution (RCE)** — attacker runs arbitrary code over the network without physical/local access — especially dangerous with admin-level impact.

### Insecure Design / Protocols
- Legacy cleartext protocols: **Telnet**, **FTP** → replace with **SSH** and **SFTP/FTPS**.

### Security Misconfiguration
- **Debug mode** left enabled on public-facing servers leaks internal app/DB details. Fix: disable debug mode outside dedicated dev environments.

### Network Vulnerabilities
- **Missing firmware updates** on network devices.
- **Cryptographic failures**:
  - SSL is obsolete — use **TLS** (careful: people say "SSL" when they mean TLS).
  - Outdated TLS versions (< 1.2) → disable, support only TLS 1.2/1.3.
  - **Insecure ciphers** (e.g., RC4) → reconfigure allowed cipher suites.
  - **Certificate problems**: name mismatch, expired cert, untrusted/unknown CA.
- **Internal IP disclosure** — misconfigured server leaks its private IP in HTTP headers despite NAT, giving attackers internal network intel.

### Critical Infrastructure / Operational Technology (OT)
- **SCADA**, **ICS**, **IoT**, physical access control, building automation (HVAC, fire suppression).
- **PLCs (Programmable Logic Controllers)** often use the **Modbus** protocol.
- IoT/OT devices are hard to patch (no auto-update, hard to get patches).
- Case study: **Mirai botnet** (Oct 2016) — hijacked IoT devices (cameras, DVRs, baby monitors) to DDoS **Dyn** (DNS provider), taking down Twitter, Amazon, NYT, etc.

### Web Application Vulnerabilities
- **Injection flaws (SQLi, XML, LDAP)** — malicious input alters a backend query. Defenses: **input validation** + **least privilege** on DB accounts.
- **Cross-Site Scripting (XSS)**:
  - **Persistent/Stored XSS** — malicious script saved on the server, served to future visitors.
  - **Reflected XSS** — script bounces off the server via a crafted query string/URL back to the victim.
- **Directory traversal** — manipulating file paths (`../../`) to reach unauthorized files. Defenses: don't expose filenames in user input, validate/block special characters, restrict storage server access.
- **File inclusion**:
  - **LFI (Local File Inclusion)** — executes a file already on the server.
  - **RFI (Remote File Inclusion)** — executes a file hosted on a remote/attacker server (more dangerous — no need to pre-stage a file locally).
  - Often used to plant a **web shell**.
- **Request forgery**:
  - **CSRF/XSRF** — tricks a logged-in user's *browser* into sending an unwanted authenticated request to another site. Defenses: anti-CSRF tokens, referrer checking.
  - **SSRF** — tricks the *server* into fetching a URL on the attacker's behalf, potentially exposing internal-only resources.
- **Identification & Authentication Failures / Broken Access Control**:
  - **Password spraying** — one common password tried against many accounts.
  - **Credential stuffing** — reused creds from one breach tried on other sites.
  - **Impersonation** — attacker takes over a legit identity (e.g., via OAuth open redirects); mitigate with strong session handling.
  - **On-path (MITM) attacks** — attacker sits between two parties intercepting/relaying traffic; mitigate with end-to-end encryption.
  - **Session hijacking** — stealing/reusing a session token/cookie; mitigate by securing session data and using encryption.
- **Data poisoning** — attacker manipulates a machine-learning **training dataset** to corrupt the resulting model's predictions.

---

## Exam Essentials Checklist
- [ ] Be able to read a scanner report and identify each section (description, solution, output, port/host, CVSS)
- [ ] Know all 8 CVSS metrics and be able to interpret a vector string (even without calculating the score by hand)
- [ ] Know the CVSS qualitative rating bands (Low/Medium/High/Critical cutoffs)
- [ ] Know the true/false positive/negative matrix cold
- [ ] Understand zero-day risk and why no patch exists
- [ ] Know the difference between LFI vs RFI, persistent vs reflected XSS, CSRF vs SSRF
- [ ] Know password spraying vs credential stuffing
- [ ] Know SCADA/ICS/IoT/PLC/Modbus basics and why OT patching is hard
- [ ] Be ready for a scenario question: "given this CVSS vector / this scan output, what's the vulnerability and how do you fix it?"
-e 

---


# Chapter 8 — Responding to Vulnerabilities
**Domain 2.0: Vulnerability Management | Objectives 2.1 (Fuzzing), 2.2 (Debuggers), 2.5**

---

## 1. Analyzing Risk

### Core Definitions (exam favorite)
- **Threat** — a possible event that could adversely affect CIA.
- **Vulnerability** — a weakness that could be exploited.
- **Risk** — exists only at the **intersection** of a threat AND a matching vulnerability. No vulnerability + threat = no risk (and vice versa).

### Risk Calculation
**Risk Severity = Probability × Magnitude** (conceptual, not always literal multiplication)
- **Probability** — likelihood the risk occurs.
- **Magnitude/Impact** — how bad it is if it does.

### Business Impact Analysis (BIA) — Quantitative vs. Qualitative
**Quantitative Risk Assessment** — 5-step numeric formula (memorize this — it's a classic PBQ):
1. **Asset Value (AV)** — $ value of the asset.
2. **Annualized Rate of Occurrence (ARO)** — expected times/year the risk occurs (e.g., 2/year = 2.0; once per 100 years = 0.01).
3. **Exposure Factor (EF)** — % of the asset that would be damaged/lost (as a percentage).
4. **Single Loss Expectancy (SLE) = AV × EF**
5. **Annualized Loss Expectancy (ALE) = SLE × ARO**

> Rule of thumb: don't spend more per year on a control than the ALE it addresses.

**Qualitative Risk Assessment** — uses subjective Low/Medium/High ratings instead of numbers; good for hard-to-quantify risks (reputation, morale, safety). Many orgs blend quantitative + qualitative.

### Supply Chain / Vendor Risk
- Vendors (cloud providers, hardware suppliers) are part of your risk surface — do vendor due diligence.
- **Hardware source authenticity** — verifying hardware wasn't tampered with in transit (reference: Snowden-leaked NSA hardware interdiction reports).

---

## 2. Managing Risk — The Four Strategies (memorize cold)
| Strategy | What it means | Example |
|---|---|---|
| **Risk Mitigation** | Apply controls to reduce probability and/or magnitude. Most common strategy. | Cable locks for laptops; buying more DDoS bandwidth/capacity |
| **Risk Avoidance** | Change business practice to eliminate the risk entirely — often has serious business drawbacks | Banning laptops; shutting down the website |
| **Risk Transference** | Shift impact to a third party | Buying insurance (property or cyber) |
| **Risk Acceptance** | Deliberate, analyzed decision to do nothing further | Cost of mitigation > cost of risk, so you just absorb losses when they occur |

⚠️ Risk acceptance must be a **deliberate, documented decision** — doing nothing without analysis is just an *unmanaged* risk, not accepted risk.

---

## 3. Security Controls

### Control Categories (mechanism of action)
- **Technical** — firewalls, ACLs, IPS, encryption.
- **Operational** — access reviews, log monitoring, vulnerability management (day-to-day processes).
- **Managerial** — risk assessments, security planning, governance integration.

### Control Types (desired effect)
- **Preventive** — stop an issue before it happens (firewalls, encryption).
- **Detective** — identify events that already occurred (IDS).
- **Responsive** — help respond to an active incident (24×7 SOC).
- **Corrective** — remediate issues that already happened (restoring backups after ransomware).
- **Compensating** — mitigate risk from an *exception* to policy (e.g., isolating a legacy system you can't patch).

---

## 4. Threat Classification & Modeling

### STRIDE (Microsoft) — memorize the acronym
- **S**poofing of identity
- **T**ampering
- **R**epudiation
- **I**nformation disclosure
- **D**enial of service
- **E**levation of privilege

Other models mentioned: **PASTA**, **LINDDUN**, CVSS, attack trees, security cards.

### Threat Modeling Elements
Adversary capability • total attack surface • possible attack vectors • impact if successful • likelihood of success.

### Threat Research Types
- **Threat reputation** — reputation of a site/IP/domain/actor (e.g., Cisco Talos).
- **Behavioral assessment** — especially useful for **insider threats** (privileged account abuse, off-hours activity, shared password use).
- **Indicators of Compromise (IOCs)** — forensic evidence used *after* an attack has started (possibly still ongoing).

---

## 5. Managing the Computing Environment

### Attack Surface Management
- **Edge discovery** — scan your own public IP ranges to find exposed systems.
- **Passive discovery** — monitor traffic to spot devices missed by active scans.
- **Security controls testing** — verify controls actually work.
- **Penetration testing / adversary emulation** — simulate real attacker behavior.
- → **Attack surface reduction** = the resulting fix-up work.

### Bug Bounty Programs
- Incentivize **responsible disclosure** (vs. public disclosure, exploitation, or no action) via financial rewards. Example: Google paid $112,500 for a Pixel vulnerability (Jan 2018).

### Change & Configuration Management
- **Configuration management** — tracks OS settings + installed software inventory.
- **Baseline** — snapshot of a system at a point in time, used to detect unauthorized drift.
- **Version control** — incrementing release numbers for software/scripts.
- **Change management** — formal process to request/approve/implement changes.
- **Maintenance windows** — scheduled low-activity periods (evenings/weekends) for batching changes, coordinated by a change manager.

### Patch Management
- Windows Update, Linux package managers, etc. Configuration management tools help track/automate/verify patch application.

---

## 6. Software Assurance / SDLC (Objective 2.5)

### SDLC Phases (generic model)
Feasibility → Requirements/Analysis → Design → Development (coding) → Testing/Integration (incl. UAT) → Training & Transition → Operations & Maintenance → Disposition/Decommissioning.

### Environments
**Development → Test/Staging → Production**, governed by change management (with rollback capability).

### SDLC Models
| Model | Key trait |
|---|---|
| **Waterfall** | Strictly sequential, 6 phases, no overlap. Best for fixed scope/timeline, stable tech. Inflexible. |
| **Spiral** | Waterfall + iteration; 4 repeated phases (Identification, Design, Build, Evaluation/Risk Analysis); heavy emphasis on **risk assessment** each cycle. |
| **Agile** | Iterative/incremental, based on the Agile Manifesto (4 values, 12 principles). Uses **sprints**, **backlogs**, **planning poker**, **timeboxing**, **user stories**, **velocity tracking**. |
| **RAD (Rapid Application Development)** | No separate planning phase — prototyping-driven; 5 phases: business modeling, data modeling, process modeling, application generation, testing & turnover. |

### DevOps / DevSecOps
- **DevOps** — merges dev + ops via toolchains to streamline the whole SDLC.
- **DevSecOps** — security baked in as a shared responsibility throughout.
- **CI (Continuous Integration)** — frequent code check-ins to a shared repo + automated builds.
- **CD (Continuous Deployment/Delivery)** — automatically ships tested changes to production.
- CI/CD needs automated security testing built into the pipeline — risk of untrusted code sneaking in and being reverted before detection.

### Common Software Security Issues (know these by name)
- Improper error handling (leaks stack traces/internal info)
- Dereferencing issues (e.g., **null pointer dereference**)
- Insecure (direct) object references
- Race conditions
- Broken authentication
- Sensitive data exposure
- Insecure/vulnerable components
- Insufficient logging & monitoring
- Weak/default configurations (e.g., default passwords)
- Insecure functions (e.g., `strcpy` — no bounds checking → buffer overflow risk)

### Secure Coding Best Practices (Objective 2.5 — memorize this list)
- **Input validation**
- **Output encoding**
- **Secure session management**
- **Authentication** (use MFA)
- **Data protection** (encryption at rest/in transit)
- **Parameterized queries** (precompiled SQL — defeats SQLi)

### Software Security Testing Techniques
| Technique | What it does |
|---|---|
| **Static code analysis** | Reviews source code without executing it — "white-box"; finds logic issues other tests miss |
| **Dynamic code analysis** | Executes the code with test input |
| **Fuzzing** | Sends random/invalid data to find crashes, input validation issues, memory leaks — usually automated, catches only simple issues |
| **Fault injection** | Deliberately injects faults into rarely-used error-handling paths (compile-time, protocol-based, or runtime injection) |
| **Mutation testing** | Makes small code changes ("mutants") and tests whether they're caught/rejected — validates test suite quality |
| **Stress/load testing** | Simulates full (or beyond-full) production load to find breaking points |
| **Security regression testing** | Confirms a patch/update didn't reintroduce old vulnerabilities or add new ones |
| **User Acceptance Testing (UAT)** | Business users validate the software meets functional/usability needs |

### Debuggers (Objective 2.2)
- **Immunity Debugger** — built for pen testing & malware reverse engineering.
- **GDB (GNU Debugger)** — open-source, Linux, multi-language.
- **Decompilation** — reversing an executable back to source code; difficult, rarely fully successful.

---

## 7. Policies, Governance, and SLOs

### The Policy Framework Hierarchy (mandatory vs. optional — exam loves this distinction)
| Document | Mandatory? | Purpose |
|---|---|---|
| **Policy** | ✅ Mandatory | High-level statement of management intent (approved at CEO/exec level) |
| **Standard** | ✅ Mandatory | Detailed implementation requirements (lower-level approval, changes more often) |
| **Procedure** | ✅ Mandatory | Step-by-step instructions for a specific task |
| **Guideline** | ❌ Optional | Best-practice advice/recommendations |

### Common Policy Library Documents
Information security policy, Acceptable Use Policy (AUP), data ownership policy, data classification policy, data retention policy, account management policy, password policy, continuous monitoring policy, code of conduct/ethics.

### Service Level Objectives (SLOs) & SLAs
- **SLO** — formal target, e.g., "99.999% uptime" ("five nines" ≈ <6 min downtime/year).
- **SLA** — the contract document containing SLOs, often with financial penalties for missing them.

### Exceptions & Compensating Controls
- Exception requests should document: the requirement being excepted, reason for noncompliance, business/technical justification, scope & duration, associated risks, supplemental/compensating controls, remediation plan, and any unmitigated risk.
- **PCI DSS's 3-part test for a valid compensating control:**
  1. Meets the **intent and rigor** of the original requirement.
  2. Provides a **similar level of defense**, sufficiently offsetting the risk.
  3. Is **"above and beyond"** other existing PCI DSS requirements.

---

## Exam Essentials Checklist
- [ ] Know risk = threat ∩ vulnerability; no risk without both.
- [ ] Be able to calculate AV, ARO, EF, SLE, ALE from a scenario (classic PBQ math).
- [ ] Know the 4 risk management strategies cold, with examples.
- [ ] Know control categories (technical/operational/managerial) vs. control types (preventive/detective/responsive/corrective/compensating).
- [ ] Know STRIDE spelled out.
- [ ] Know attack surface management activities (edge discovery, passive discovery, controls testing, pen testing).
- [ ] Know Waterfall vs. Spiral vs. Agile vs. RAD differences.
- [ ] Know static vs. dynamic code analysis, plus fuzzing, fault injection, mutation testing, regression testing.
- [ ] Know Immunity Debugger vs. GDB.
- [ ] Know Policy > Standard > Procedure (all mandatory) vs. Guideline (optional).
- [ ] Know the PCI DSS 3-part compensating control test.
