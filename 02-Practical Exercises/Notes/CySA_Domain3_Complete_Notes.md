# CompTIA CySA+ (CS0-003) — Domain 3.0: Incident Response and Management
### Combined Study Notes — Chapters 9, 10, 11 (20% of exam weight)

---

# Chapter 9 — Building an Incident Response Program
**Domain 3.0: Incident Response and Management | Objectives 3.1, 3.2, 3.3**

---

## 1. Security Incidents — Core Terminology (memorize the distinctions)
| Term | Definition |
|---|---|
| **Event** | Any observable occurrence in a system/network (e.g., a user opening a file). |
| **Adverse event** | An event with negative consequences (malware infection, server crash, unauthorized file access). |
| **Security incident** | A **violation or imminent threat of violation** of security policy, AUP, or standard security practices (data loss, intrusion, keylogger, DoS attack). |

> Every incident includes one or more events, but not every event is an incident.

**CSIRT** = Computer Security Incident Response Team — the group that responds to incidents using standardized procedures + professional judgment.

---

## 2. The Four Phases of Incident Response (NIST SP 800-61) — memorize cold
**Preparation → Detection & Analysis → Containment, Eradication & Recovery → Post-Incident Activity** (with loops back — NOT strictly linear; containment often loops back to detection/analysis, and post-incident loops back to preparation)

### Phase 1: Preparation
- Build IR policy, procedures, training, and toolkits *before* an incident hits.
- NIST-recommended IR toolkit: digital forensic workstations, backup devices, laptops for data collection/analysis/reporting, spare servers/network gear, blank removable media, portable printer, forensic/packet capture software, bootable trusted-tools USB media, office/evidence supplies.
- Preparation is continuous — whenever not actively responding, the team should be preparing for next time.

### Phase 2: Detection & Analysis
**NIST's 4 categories of security event indicators:**
1. **Alerts** — IDS/IPS, SIEM, antivirus, file integrity monitoring, third-party monitoring
2. **Logs** — OS, services, apps, network devices, netflow
3. **Publicly available info** — new vulns/exploits "in the wild"
4. **People** — internal or external reports of suspicious activity

**NIST recommendations to improve detection/analysis:**
- Profile networks/systems (know your baseline)
- Understand normal user/system/app behavior
- Create a logging policy (what to log, where, retention)
- Perform event correlation (typically via **SIEM**)
- **Synchronize clocks** (via **NTP**) — critical for correlating logs across systems
- Maintain an org-wide knowledge base
- Capture network traffic ASAP once an incident is suspected
- Filter information to reduce noise/clutter
- Know when/how to seek external assistance (may also involve reverse engineering malware)

### Phase 3: Containment, Eradication & Recovery
Objectives, in order:
1. Select an appropriate containment strategy
2. Implement containment to limit damage
3. Gather additional evidence (may support legal action)
4. Identify attackers/attacking systems
5. Eradicate the incident's effects and recover normal operations

### Phase 4: Post-Incident Activity
- **Forensic analysis** — reconstruct exactly what happened.
- **Root cause analysis** — understand *how* the attacker got in so controls can be fixed (skip this and the same attack recurs).
- **Lessons learned review** — best done live (meeting, not written-only), led by an **independent/objective facilitator** not involved in the response. NIST's key questions include: what happened & when; how well did staff/management perform; were procedures followed/adequate; what info was needed sooner; what should be done differently; what corrective actions/indicators to watch for next time.
- **Evidence retention** — consult legal counsel if litigation/prosecution possible; otherwise follow org retention policy (many orgs use 2 years). **US federal agencies must retain incident records ≥3 years** (National Archives GRS 3.2, Item 20).

---

## 3. Building the IR Foundation

### Relationship to BC/DR
- **Business Continuity (BC)** — keeps normal operations running *during* a disruption.
- **Disaster Recovery (DR)** — restores normal operations *after* a disruption.
- IR teams must coordinate closely with BC/DR teams.

### Policy vs. Procedures vs. Playbooks
- **Policy** — high-level, timeless, approved at the highest level (ideally CEO). States authority, scope, roles, priorities — NOT specific technologies/techniques (those change too often).
  - NIST-recommended policy elements: management commitment statement; purpose/objectives; scope; definitions of incidents/terms; org structure & roles/responsibilities/authority; severity rating scheme; CSIRT performance measures; reporting/contact info.
- **Procedures** — detailed tactical info for responders.
- **Playbooks** — step-by-step "recipes" for specific incident types (e.g., ransomware, web defacement, phishing, lost laptop, general/uncategorized). Guide response but don't replace professional judgment — responders can deviate when needed.

### Testing the IR Plan
- **Tabletop exercises** — team discusses a scenario around a table (physical/virtual).
- More advanced tests actually exercise real IR capabilities.

---

## 4. Creating the Incident Response Team

### Team Composition
- **Core team** — dedicated cybersecurity/IR professionals (may be full-time in large orgs, or part-time "wear many hats" in small orgs).
- **On-call/as-needed roles**: technical SMEs (sysadmins, DBAs, network/app experts), IT support staff, **legal counsel** (compliance, regulator communication), **HR** (employee malfeasance investigations), **PR/marketing** (media/public communication).
- **Management's role** — provide authority, resources, and time; make critical business calls (shutting down servers, communicating with law enforcement/public).
- **CSIRT Leader** — directs response, liaises with management.
- **Incident response providers** — external/outsourced IR expertise; understand their guaranteed response time and have a plan to cover the gap before they take over.

### CSIRT Scope of Control (policy should answer)
- What triggers CSIRT activation? Who can activate it?
- Does it cover the whole org or specific units?
- Is it authorized to talk to law enforcement/regulators/external parties?
- What are its internal communication/escalation responsibilities?

---

## 5. Classifying Incidents

### Threat Classification — NIST Attack Vectors (know examples for each)
| Vector | Example |
|---|---|
| **External/Removable Media** | Malware from infected USB drive |
| **Attrition** | Brute-force attacks, DDoS |
| **Web** | XSS used to steal credentials |
| **Email** | Malicious attachment or link |
| **Impersonation** | Spoofing, on-path/MITM, rogue APs, **SQL injection** |
| **Improper Usage** | AUP violation (e.g., installing file-sharing software) |
| **Loss or Theft of Equipment** | Stolen laptop/phone/token |
| **Unknown** | Origin unknown |
| **Other** | Known origin, doesn't fit elsewhere |

- **APT (Advanced Persistent Threat)** — highly skilled, well-funded (nation-state/organized crime), often exploits **zero-days** (unknown to security community, unpatched, undetected by scanners).

### Severity Classification — Two Key Measures
**A) Scope of Impact**
- **Functional Impact (NIST 4 levels):**
  | Level | Definition |
  |---|---|
  | None | No effect on service to any users |
  | Low | Minimal effect — critical services still provided, but less efficiently |
  | Medium | Lost ability to provide a critical service to a **subset** of users |
  | High | Lost ability to provide critical services to **any** users |
- **Economic Impact (custom scale, not in core NIST model):** None / Low (≤$10K) / Medium ($10K–$500K) / High (≥$500K) — thresholds should scale to org size.
- **Recoverability Effort (NIST 4 levels):**
  | Level | Definition |
  |---|---|
  | Regular | Predictable with existing resources |
  | Supplemented | Predictable with additional resources |
  | Extended | Unpredictable; needs outside help |
  | Not Recoverable | Recovery impossible (e.g., data leaked publicly) → launch investigation |

**B) Data Types (Information Impact)**
- NIST categories: None / **Privacy breach** (PII) / **Proprietary breach** (unclassified proprietary info, e.g. PCII) / **Integrity loss** (data changed/deleted).
- Private-sector alternative: None / **Regulated information breach** (PII/PHI/PCI DSS/GDPR-SPI) / **Confidential information breach** (IP, trade secrets) / **Integrity loss**.
- Know: PII, PHI (HIPAA), SPI (GDPR — includes genetic data, union membership, sexual info), high-value assets, financial info, IP, corporate info.

---

## 6. Attack Frameworks (Objective 3.1 — exam specifically requires these 3)

### MITRE ATT&CK
- "Adversarial Tactics, Techniques, and Common Knowledge" — a comprehensive, freely available knowledge base covering the **full threat lifecycle**: initial access → execution → persistence → privilege escalation → exfiltration.
- Matrices exist for: enterprise (Windows/macOS/Linux/cloud/network/containers), mobile (iOS/Android), and ICS.
- Each technique entry includes ID, tactic, platforms, mitigations, detections, associated threat actor groups.

### Diamond Model of Intrusion Analysis
- Models an event as: **Adversary** deploys a **Capability** against **Infrastructure** targeting a **Victim** (the 4 vertices of the "diamond" = **Core Features**).
- **Meta-Features**: timestamps, phase, result, direction, methodology, resources — used to sequence events into an "activity thread."
- **Confidence Value** — undefined by the model itself; analysts assign it based on their own judgment.
- Strong focus on understanding attacker motivation and relationships between elements.

### Lockheed Martin Cyber Kill Chain — 7 stages (memorize in order)
1. **Reconnaissance** — target/intel gathering
2. **Weaponization** — combine malware + exploit into a deliverable payload
3. **Delivery** — payload reaches target (email, USB, website)
4. **Exploitation** — payload triggers, exploits the vulnerability
5. **Installation** — persistent backdoor/remote access established
6. **Command-and-Control (C2)** — two-way remote control channel
7. **Actions on Objectives** — attacker achieves their goal (data theft, damage, lateral movement, privilege escalation)

**Criticism (exam-testable):** the Kill Chain has been criticized for including actions **outside the defended network** (where defenders can't act), for over-focusing on perimeter/anti-malware defenses, and for **under-addressing insider threats**.

*(Not on the exam objectives, but mentioned: the Unified Kill Chain combines Cyber Kill Chain + ATT&CK into an 18-phase model.)*

---

## 7. Testing Strategy Standards (Objective 3.1 — know these 2 by name)
- **OSS TMM (Open Source Security Testing Methodology Manual)** — published by ISECOM; covers testing security of physical locations, human interactions, and communications (broader than just networks/apps).
- **OWASP Web Security Testing Guide** — focused specifically on web application security testing.

---

## Exam Essentials Checklist
- [ ] Know event vs. adverse event vs. security incident precisely
- [ ] Know the 4 IR phases in order, and that the process loops (not strictly linear)
- [ ] Know NIST's 4 categories of security event indicators
- [ ] Know NTP's role in log correlation
- [ ] Know policy (mandatory, high-level, CEO-approved) vs. procedure vs. playbook
- [ ] Know who's typically on a CSIRT (core + as-needed SMEs, legal, HR, PR)
- [ ] Know NIST's attack vectors and be able to classify a scenario (e.g., XSS = Web, SQLi = Impersonation)
- [ ] Know functional impact levels (None/Low/Medium/High) and recoverability levels (Regular/Supplemented/Extended/Not Recoverable)
- [ ] Know data/information impact categories (Privacy breach, Proprietary breach, Integrity loss / Regulated vs. Confidential info breach)
- [ ] Know MITRE ATT&CK, Diamond Model, and Cyber Kill Chain (7 stages) in detail — these 3 are explicitly named in the exam objectives
- [ ] Know the Kill Chain's main criticisms
- [ ] Know OSSTMM vs. OWASP Testing Guide purposes
-e 

---


# Chapter 10 — Incident Detection and Analysis
**Domain 3.0: Incident Response and Management | Objective 3.2 (Detection and Analysis)**

---

## 1. Indicators of Compromise (IoCs) — Overview

**IoC** = information about activity/events/behaviors commonly associated with malicious behavior.

> **IoC vs. IoA** (not on exam objectives, but useful distinction): **IoAs** (Indicators of Attack) can be spotted *while an attack is happening*. **IoCs** are more forensic — evidence gathered about what occurred. In practice the terms blur together.

### Common IoC Categories (NIST-style list — know this list cold)
- Unusual network traffic (outbound, peer-to-peer, unexpected ports/IPs)
- Increases in database or file share read volume
- Suspicious changes to filesystems, Windows Registry, config files
- Traffic patterns unusual for human usage
- Login/rights usage irregularities (geographic & time-based anomalies)
- Denial-of-service activity/artifacts
- Unusual DNS traffic

### IoC Feeds
- Provide community threat data: malicious IPs/hostnames, malware C2 domains, malware hashes, behavior-based threat actor info.
- Example: **AlienVault Open Threat Exchange (OTX)** — millions of community-submitted IoCs ("pulses").
- Both **commercial** and **free/open** feeds exist — evaluate reliability before acting on any feed.

---

## 2. Investigating Specific IoC Types

### Unusual Network Traffic
- **Abnormal service ports** — services should run on well-known/documented ports; traffic on unexpected ports may indicate compromise.
- ⚠️ **Source ports are randomized** in most traffic — don't waste time chasing "unusual" source ports; that's a rookie mistake.
- Watch for: peer-to-peer traffic within a datacenter (where systems should only talk outbound), port/vulnerability scanning traffic, traffic that just doesn't fit normal patterns.
- **Outbound traffic indicators**: traffic to unexpected locations; unusual traffic types (RDP, SSH, file transfers) leaving the network; unusual volume; outbound DNS queries to flagged/unexpected domains; traffic at unusual times.

### Increases in Resource Usage
- Attackers consume CPU/memory (running tools), disk space (staging exfil data), and network bandwidth (transferring data, scanning).
- **Database read volume spikes** — can indicate data harvesting, but isn't proof by itself; combine with other IoCs.

### Unusual User and Account Behaviors
- **Privileged account misuse** — critical to monitor, though rare legitimate admin actions can trigger false alarms.
- **Privilege escalation / new group memberships** — flag any newly granted admin/elevated rights.
- **Bot-like behavior** — commands executed faster than humanly possible, or one account logging into many systems in rapid succession.
- Requires a solid baseline of "normal" job-related behavior to separate malicious activity from legitimate rare actions.

### File and Configuration Modifications
- Tools: **OSSEC** (Open Source HIDS SECurity) and **Tripwire** — host-based intrusion detection systems (HIDS) that monitor unauthorized filesystem changes.
- Watch for: unexpected data aggregation (staging for exfiltration) and even **unexpected patching** — attackers sometimes patch the very flaw they exploited, to keep other attackers out.

### Login and Rights Usage Anomalies
- **Impossible travel** — the classic example: a user logs in from one location, then logs in again from a location hundreds of miles away shortly after. Strong IoC even if not always malicious (VPNs can cause false positives).
- **Time-based anomalies** — off-hours logins/activity relative to a user's normal schedule. Can produce false positives for staff with irregular hours — tune accordingly.

### Denial of Service (DoS/DDoS)
- **DDoS** — traffic from many distributed sources (often a botnet) — hard to identify, attribute, and stop.
- **Amplification attacks** — exploit protocol behavior to multiply attack volume without needing to fully compromise the abused system.
  - **DNS amplification** — attacker sends small spoofed queries to open DNS resolvers, which reply with much larger responses directed at the victim.
- Repeated requests for the same file/directory can *look* like DoS but is often actually exploit/scanning activity, not a true DoS.

### Unusual DNS Traffic
- Abnormal DNS query volume, especially to unusual/random-looking domains (e.g., `jku845.com` — often signs of a **Domain Generation Algorithm, DGA**).
- Large numbers of DNS query **failures** — may indicate DGA-based malware trying many auto-generated names.
- **DNS tunneling** — attacker encodes C2 traffic or data inside DNS queries/responses to sneak past network controls.
- **Fast-flux DNS** — rapidly rotating IP addresses for a domain, used to keep malicious C2 infrastructure resilient even as individual hosts get taken down.
- ⚠️ Don't blanket-allowlist your own security team's systems from IoC rules — doing so could let a compromise on those very systems go undetected.

### Combining IoCs
A single IoC is rarely conclusive on its own — real detection usually requires **correlating multiple IoCs** together (logs + threat feeds + behavioral anomalies) to confidently identify a compromise.

---

## 3. Evidence Acquisition and Preservation (Objective 3.2)

### Preservation
Requires: acquiring evidence → validating the acquisition/data → storing it securely with documentation. If evidence may be used legally or with law enforcement, **chain-of-custody** documentation becomes mandatory.

### Chain of Custody
Tracks evidence through its entire life cycle: collection → preservation → analysis. Must document **who** accessed the data, **when**, **where**, **why**, and **how** it was stored/used/transferred. Prevents allegations that evidence was tampered with.

### Legal Hold
- Part of **eDiscovery**. Legal counsel issues a hold notice when litigation is starting/underway.
- Data custodians must **preserve data that would otherwise be deleted** under normal retention/purge policies.
- Organizations may also self-initiate a hold proactively if litigation is anticipated.

### Validating Data Integrity
- Confirms the copy/image matches the original exactly (capture process didn't alter anything).
- Done via **hashing** — compute a hash of the original, then of the copy, and compare. (Tool example: **FTK Imager**.)

---

## Exam Essentials Checklist
- [ ] Know the full list of common IoC categories
- [ ] Know why source ports are a poor IoC signal (randomization) vs. destination/service ports
- [ ] Know "impossible travel" as the classic geographic/time-based anomaly example
- [ ] Know OSSEC and Tripwire as HIDS/file-integrity monitoring tools
- [ ] Know DDoS vs. amplification attacks (e.g., DNS amplification)
- [ ] Know DGA, DNS tunneling, and fast-flux DNS as DNS-based IoCs
- [ ] Understand why combining multiple IoCs is more reliable than any single indicator
- [ ] Know chain of custody, legal hold, and hashing-based integrity validation for evidence handling
- [ ] Practice scenario questions: given a log snippet or traffic description, identify which IoC category it represents
-e 

---


# Chapter 11 — Containment, Eradication, and Recovery
**Domain 3.0: Incident Response and Management | Objective 3.2 (Containment, Eradication, and Recovery)**

---

## 1. Containment — The First Active Response

Once an incident is confirmed, containment should begin **as quickly as possible**. It shifts the CSIRT from passive detection/analysis into active response.

- **Scope** = number of systems/individuals involved.
- **Impact** = effect on the organization.
- Containment limits **both** — think of it as "building a fence" around the incident.

> ⚠️ **Exam Note:** Containment is the top priority — stop the spread *before* worrying about eradication or recovery.

### Containment Depends on Incident Type
- **Data exfiltration** (e.g., credit card system) → disconnect the system from the network to stop the bleeding.
- **DDoS attack** → disconnecting won't help (that's what the attacker wants!) — instead, filter upstream traffic or block by signature.

### NIST's Containment Strategy Criteria (memorize this list)
1. Potential damage to / theft of resources
2. Need for evidence preservation
3. Service availability (network connectivity, external services)
4. Time and resources needed to implement the strategy
5. Effectiveness of the strategy (partial vs. full containment)
6. Duration of the solution (emergency workaround vs. temporary vs. permanent fix)

*(Note: "cost" and "log records generated" are NOT on this official list — a classic exam distractor.)*

There's no formula — responders must weigh these criteria against business needs and management's intent, using professional judgment.

---

## 2. Containment Techniques — Segmentation, Isolation, Removal (know the *escalating* order)

### Segmentation
- Placing suspect systems on a separate **VLAN** ("quarantine network") connected to the firewall, allowing **limited** access to other systems.
- Still allows some access — useful when responders want to keep observing behavior while limiting exposure.
- Same concept used proactively (defense-in-depth) as well as reactively (during IR).

### Isolation
Two forms:
1. **Isolating affected systems** — going further than segmentation: quarantine network connects **directly to the Internet only**, cut off from all other internal networks. Attacker can still reach/control the system (useful for continued observation), but it can't touch anything else internally.
   - The "airgapped system" concept (used outside IR too) is related — fully isolated from other networks.
2. **Isolating the attacker** — using **sandboxes** or **honeypots**: environments built purely to observe attacker behavior, containing nothing of real value.

⚠️ **Risk of segmentation/isolation**: the attacker retains access to the compromised system and could pivot to attack third parties over the Internet — creating legal/liability exposure. Always consult **management and legal counsel** before choosing this path.

### Removal (the strongest technique)
- Completely disconnects affected systems from **all** networks — potentially even from each other (physical disconnection).
- ⚠️ **Not foolproof** — NIST's classic example: an attacker sets up a periodic ping to a reliable external host (e.g., Google DNS `8.8.8.8`) as a "dead man's switch." If the pings start failing (system removed from network), a script triggers to wipe evidence or encrypt data before responders can investigate.

**Order of increasing severity: Segmentation → Isolation → Removal.**

---

## 3. Evidence Acquisition During Containment
- Even though damage limitation is the priority, gather evidence along the way — it supports later analysis and potential legal action.
- NIST-recommended **evidence log** contents:
  - Identifying info (location, serial #, model #, hostname, MAC/IP addresses)
  - Name, title, phone number of everyone who collected/handled evidence
  - Time and date (with time zone) of each handling event
  - Storage locations of the evidence
- Sloppy logs → chain-of-custody gets challenged → evidence may become **inadmissible in court**.

---

## 4. Identifying Attackers (Objective 3.2 — nuanced exam topic)
- Attackers relay through compromised systems across borders — tracing true origin is very difficult.
- **NIST's own guidance**: identifying the attacker "can be a time-consuming and futile process that can prevent a team from achieving its primary goal — minimizing the business impact."
- **Business priority vs. law enforcement priority conflict**: law enforcement wants to identify/prosecute; the organization's priority is containment/eradication/recovery. These goals can conflict — weigh carefully before/while looping in law enforcement.
- Law enforcement has tools private analysts don't (search warrants on ISPs, access to sensitive threat databases) — worth involving them if attribution truly matters.

---

## 5. Eradication and Recovery

### Eradication
Removing all remaining artifacts of the incident: malicious code, sanitizing compromised media, securing compromised accounts.

### Recovery
Restoring normal operations: rebuilding/patching systems, reconfiguring firewalls, updating malware signatures — done in a way that **reduces the chance of a repeat attack**, not just restoring service.

### Root Cause Analysis (again — critical link between eradication/recovery)
- Must understand *how* the attacker got in, or the same hole stays open.
- Distinct from "identifying the attacker" — root cause analysis is valuable; attacker attribution often isn't.
- Root cause findings often reveal the same vulnerability exists elsewhere (e.g., a misconfigured router model used across the org) — fix it everywhere, not just on the compromised system.

### Remediation and Reimaging
- Once compromised, a system should be considered **completely untrustworthy** — don't just "fix and move on."
- Rebuild from scratch or restore from a known-good backup/image.
- ⚠️ If the compromise was due to a **vulnerability** (not just a stolen account), backups/images likely carry that same vulnerability forward — remediate it before/during restoration, or you'll get re-compromised the same way.

### Patching Priority Order (Figure 11.6 concept)
1. Systems **directly** involved in the compromise (patch first)
2. Systems **ancillary/related** to the compromise
3. **Other** systems across the enterprise (general patch review sweep)

---

## 6. Sanitization and Secure Disposal (NIST SP 800-88)
Three levels, in increasing order of effectiveness/difficulty/cost:

| Level | Definition | Example Techniques |
|---|---|---|
| **Clear** | Logical techniques protecting against simple, non-invasive recovery | Standard read/write overwrite, factory reset |
| **Purge** | Physical/logical techniques defeating even state-of-the-art lab recovery attempts | Overwriting, block erase, cryptographic erase, **degaussing** |
| **Destroy** | Renders media physically unusable, recovery infeasible | Disintegration, pulverization, melting, incineration |

- Decision flow considers: **security categorization** (Low/Moderate/High), whether media is **leaving organizational control**, and whether media will be **reused**.
- Always follow disposal with a **Validation** step (confirm no remnant data) and **Document** the process.

---

## 7. Validating Recovery — 4 Core Activities (memorize this checklist)
1. **Validate only authorized accounts exist** on every system/app (leverage existing periodic account reviews).
2. **Verify restored permissions** match least privilege (for users, admins, AND service accounts).
3. **Verify system/data integrity** — confirm proper configuration, no unauthorized changes; may require restoring from clean pre-incident backups.
4. **Verify logging is functioning properly** — logs flowing correctly to the centralized repository per policy.
5. *(Related but separate)* **Run vulnerability scans** on all systems to confirm no lingering exposure and kick off remediation workflows where needed.

---

## 8. Wrapping Up the Response (Post-Incident, revisited from Ch. 9)

### Managing Change Control
- Emergency response often bypasses normal change/configuration management — go back afterward and formally document all emergency changes through the standard change process.

### Lessons Learned Session
- Held after every incident (see Ch. 9 for full detail on facilitation).
- Should explicitly surface any **new IoCs** discovered during the investigation, with recommendations to add them to ongoing security monitoring.

### Final Incident Report — Key Elements (memorize this list)
- Chronology of events (incident + response)
- Root cause of the incident
- Location/description of evidence collected
- Specific containment/eradication/recovery actions taken, **with rationale**
- Impact estimates on the organization/stakeholders
- Results of post-recovery validation efforts
- Documentation of lessons-learned findings
- Should be classified per the org's data classification policy, securely stored, and destroyed per a defined retention period.

### Evidence Retention (final disposition decision)
- No longer needed → destroy per data disposal procedures.
- Needed for future use / possible legal action → secure evidence repository, chain of custody maintained.
- Depends on likelihood of criminal/civil action and incident impact — should be addressed directly in IR procedures.

---

## Exam Essentials Checklist
- [ ] Know containment is priority #1, before eradication/recovery
- [ ] Know the 6 NIST containment strategy criteria (and that "cost" and "generated logs" are NOT on the list)
- [ ] Know segmentation → isolation → removal as an escalating scale, with concrete examples of each
- [ ] Know the "dead man's switch" example of why removal isn't foolproof
- [ ] Know sandbox/honeypot = isolating the attacker
- [ ] Know why identifying the attacker is often a low-priority "distraction" per NIST, and the law enforcement conflict of interest
- [ ] Know root cause analysis is distinct from (and more valuable than) attacker identification
- [ ] Know why compromised systems must be rebuilt, not just patched in place
- [ ] Know patching priority order: directly involved → ancillary → everything else
- [ ] Know Clear vs. Purge vs. Destroy (NIST SP 800-88) with example techniques
- [ ] Know the 4 recovery validation activities + vulnerability scanning
- [ ] Know the final incident report's required elements
