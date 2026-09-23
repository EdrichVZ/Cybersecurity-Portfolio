# CompTIA CySA+ (CS0-003) — Domain 4.0: Reporting and Communication
### Complete Study Notes — Chapter 12 (17% of exam weight)

---

# Chapter 12 — Reporting and Communication
**Domain 4.0: Reporting and Communication | Objectives 4.1, 4.2 (100% of Domain 4)**

---

## PART 1: Vulnerability Management Reporting & Communication (Objective 4.1)

### Vulnerability Management Reports — Core Elements (memorize this list — directly from exam objectives)
- **Vulnerabilities** — CVE number, name, description
- **Affected hosts** — IP address + hostname (if resolvable)
- **Risk score** — a qualitative measure of severity **in organizational context**
- **Mitigation** options — patches, updates, workarounds
- **Recurrence** — if a vuln reappears, that's a red flag something went wrong upstream (e.g., a broken build image)
- **Prioritization** — informed by risk/CVSS scores + org policy

> ⚠️ **CVSS ≠ Risk Score.** CVSS gives a **vulnerability score** based on a structured, context-free ranking. A **risk score** incorporates organizational context (system exposure, importance) — a low-CVSS issue on a critical exposed asset might get a higher risk priority, and vice versa.

Reports are typically **automated** given their volume/frequency; automated patching + centralized management tools ease the remediation burden.

### Stakeholder Categories (4 types — know these)
1. **Technical stakeholders** — need vuln details, remediation/mitigation options, prioritization to do the actual work.
2. **Security/audit/compliance stakeholders** — need overall vuln posture, recurrence trends.
3. **Security management/oversight systems** — need standardized, automated feeds (APIs) for broader security context.
4. **Executive/leadership** — need dashboard-level summaries for oversight.

*(Same underlying data, different views/summaries per audience — not separate reports from scratch.)*

### Compliance Reports
- Specialized reports aligned to standards like **PCI DSS**; custom-built reports needed for non-standard compliance obligations.
- Provided to certifying bodies or retained as compliance proof; done on a regular, recurring basis.

### Action Plans (Objective 4.1 — exam-listed items, memorize this set)
- **Configuration management** — hardening, removing default configs, baseline definitions.
- **Patching** — must account for business processes; requires **testing before production** deployment (vendor patches aren't always safe — e.g., Windows 10's Oct. 2018 update 1809 deleted user files!). Orgs often wait for community experience with a patch before wide deployment, unless the security risk is too high to wait.
- **Compensating controls** — used when a patch doesn't exist/can't be installed/is flawed (e.g., WAF rules blocking a known exploit, or disabling a vulnerable service). Must be **documented** and **flagged** in vuln management systems (otherwise the vuln keeps re-appearing in reports) and should have a **review period**, especially if intended as temporary.
- **Awareness, education, and training** — ongoing for sysadmins, security staff, leadership, and auditors alike.
- **Changing business requirements** — less frequent, but vuln management practices must adapt as the business evolves.

### Vulnerability Management Metrics & KPIs (Objective 4.1 — memorize these 5)
| Metric | Notes |
|---|---|
| **Trends** | # of vulns, severity/risk rating, time to remediate; recurrence should trend to zero |
| **Top 10 lists** | Useful for focusing effort, but arbitrary — don't rely on this alone; more than 10 critical items may exist |
| **Critical vulnerabilities** | Typically CVSS 9.0–10.0; track count + time-to-patch |
| **Zero-days** | Vulnerabilities announced before a patch exists — hard to track via normal vuln scanning since no detection signature exists yet; response relies on compensating controls until a patch lands |
| **SLOs** | Specific targets like time-to-remediate; measuring whether SLOs are met is core to SLA management |

### Inhibitors to Remediation (Objective 4.1 — memorize this full list, classic exam scenario topic)
| Inhibitor | Why it delays remediation |
|---|---|
| **MOUs (Memorandums of Understanding)** | May set uptime/performance targets or restrict who can touch a system — common with embedded/specialized systems |
| **SLAs (Service Level Agreements)** | Uptime/performance terms may push orgs to delay patching |
| **Organizational governance** | Business process/validation requirements slow things down |
| **Business process interruption** | Patching requires downtime some systems can't tolerate |
| **Degrading functionality** | A patch might disable/change a service or break old protocol support |
| **Legacy systems** | No patches available at all → compensating controls may be the only option |
| **Proprietary systems** | Vendor restrictions on patch versions may conflict with vendor support terms |

> Governance/SLA/MOU issues can often be fixed via **process change**; business-interruption/legacy/proprietary issues often need **infrastructure or design changes** instead.

---

## PART 2: Incident Response Reporting & Communication (Objective 4.2)

> **Key distinction:** Communication happens **throughout** the entire IR process; formal **reporting** is most associated with the **post-incident activity** phase (built on root cause analysis + lessons learned).

### Stakeholder Identification
- **Internal**: incident responders, technical staff (sysadmins/devs), management, legal counsel, communications/marketing.
- **External**: customers, service providers, law enforcement, external counsel, regulators/government agencies, media.

### Incident Declaration and Escalation (the communication trigger sequence)
1. IoCs are communicated to responders.
2. Responders determine: real incident or false positive?
3. If real → **incident declared**, IR process + communications plan activated → moves into containment/eradication/recovery.

### External Communications (NIST SP 800-61 — 4 categories, all exam-relevant)

**Legal**
- Internal counsel — advice on sensitive data/compliance/HR matters.
- External counsel — engaged for anticipated legal action or specialized advice.
- Engagement is a **deliberate decision**, not automatic — establish the relationship *before* you need it.

**Public Relations** — two exam-listed sub-topics:
- **Customer Communication** — balancing transparency/trust against risks of premature/incorrect info during an active investigation. Define practices in advance: who communicates, what, when, how.
- **Media** — NIST recommends: a **single point of contact** (+ backup) for media, media training for spokespeople, a maintained incident status statement for consistency, and practice sessions during IR exercises. Communication may be **mandatory** (regulatory), **voluntary**, or **forced** (media already covering it).

**Regulatory Reporting**
- Example: US **CIRCIA (Cyber Incident Reporting for Critical Infrastructure Act of 2022)** — requires reporting substantial incidents to **CISA within 72 hours**, and **ransomware payments within 24 hours**.
- Requirements vary by location/industry — ongoing legal review needed.

**Law Enforcement**
- May be involved for criminal incidents or nation-state actors beyond the org's ability to handle alone.
- ⚠️ Involving law enforcement can **change the IR process** significantly (e.g., they may seize systems). Always decide **with legal counsel's advice**.
- NIST: predetermine communication guidelines in advance since these communications often must happen **quickly**.

### Root Cause Analysis (RCA) — 4-Step Process (memorize this)
1. **Identify** the problems/events that occurred (describe as fully as possible).
2. **Build a timeline** of events (what happened, in what order).
3. **Differentiate** root cause(s) vs. results of the root cause vs. **causal factors** (contributing but not root).
4. **Document** the RCA (often as a diagram/chart).

> NIST (SP 800-30/800-39) defines RCA conceptually but doesn't prescribe *how* to do it — that's left to the practitioner.

### Lessons Learned
- Focused on **preventing recurrence**, not assigning blame.
- Findings should drive real changes/controls (see also Ch. 9's facilitation details).

### Incident Response Metrics & KPIs (Objective 4.2 — memorize these 4)
| Metric | Definition / Notes |
|---|---|
| **Mean Time to Detect (MTTD)** | Time from initial compromise event to detection. Needs forensic analysis to measure accurately. APT victims often show terrible MTTD (compromised for months/years undetected). |
| **Mean Time to Respond (MTTR - respond)** | Time from detection to formally declaring an incident and activating the IR process. |
| **Mean Time to Remediate (MTTR - remediate)** | Time to fully resolve — highly variable by incident size/complexity; needs nuanced, contextual reporting rather than a single raw number. |
| **Alert Volume** | Weakest/least useful metric — high or low volume can mean many different (even contradictory) things about program effectiveness. Better to measure whether alerts actually triggered IR activation vs. incidents being found by other means. |

### Incident Response Reports — Key Components (memorize — direct exam objective list)
- **Executive summary** — short, clear, plain-language overview of incident/impact/status.
- **The 5 W's** — Who, What, When, Where, Why.
- **Recommendations** — from lessons learned: what went well, what to improve, corrective actions.
- **Timeline** — sequence of events; reveals response delays and attacker methodology.
- **Impact** — financial, reputational, or other damage assessment.
- **Scope** — which systems/services/parts of the org were affected.
- **Evidence** — gathered during investigation, often attached as an appendix.

Reference: **CISA/DHS incident reporting template** (Appendix D-style form) — includes incident priority, incident type (compromised system, DoS, malware, phishing, policy violation, physical break-in, reconnaissance, etc.), and full timeline fields.

---

## Exam Essentials Checklist
- [ ] Know the vulnerability report core elements (CVE, affected hosts, risk score, mitigation, recurrence, prioritization) and that risk score ≠ CVSS score
- [ ] Know the 4 stakeholder categories for vuln management reporting
- [ ] Know the vuln management action plan items (config mgmt, patching, compensating controls, training, changing business requirements)
- [ ] Know vuln management KPIs: trends, top 10, critical vulns, zero-days, SLOs
- [ ] Know all 7 inhibitors to remediation (MOU, SLA, governance, business interruption, degraded functionality, legacy systems, proprietary systems)
- [ ] Know that communication happens throughout IR, while formal reporting centers on post-incident activity
- [ ] Know the 4 external communication categories: Legal, PR (customer + media), Regulatory, Law Enforcement
- [ ] Know CIRCIA's 72-hour / 24-hour reporting windows
- [ ] Know the 4-step root cause analysis process
- [ ] Know the 4 IR metrics: MTTD, MTTR (respond), MTTR (remediate), alert volume — and why alert volume is the weakest
- [ ] Know the required IR report components: exec summary, 5 W's, recommendations, timeline, impact, scope, evidence
