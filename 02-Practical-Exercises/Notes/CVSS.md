
## CVSS Severity Ranges & Operational Responses

The **Common Vulnerability Scoring System (CVSS)** provides a standardized way of rating the severity of vulnerabilities. The score helps security teams prioritize vulnerabilities, but the final response should also consider factors such as exploit activity, asset criticality, and exposure.

| Severity        |     CVSS Score | Typical Response     | Recommended Operational Action                                                                                                               |
| --------------- | -------------: | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| 🔴 **Critical** | **9.0 – 10.0** | Immediate            | Prioritize for immediate remediation. Apply patches, isolate affected systems, or implement temporary mitigations if a patch is unavailable. |
| 🟠 **High**     |  **7.0 – 8.9** | Urgent               | Prioritize during the next deployment or patching cycle. Investigate exposure and apply appropriate security updates or mitigations.         |
| 🟡 **Medium**   |  **4.0 – 6.9** | Routine              | Address through scheduled patching and maintenance. Assess the vulnerability in relation to the affected system and potential attack paths.  |
| 🔵 **Low**      |  **0.1 – 3.9** | Deferred / As Needed | Monitor and remediate according to organizational risk tolerance and normal maintenance cycles.                                              |
| ⚪ **None**      |        **0.0** | No Action            | Document the finding and determine whether any additional security action is required.                                                       |

### Real-World Context

CVSS should **not be used as the only factor** when determining vulnerability priority.

A vulnerability with a lower CVSS score may require more urgent attention than a Critical vulnerability if it is actively being exploited or affects an important internet-facing system.

When prioritizing vulnerabilities, consider:

1. **Exploit Activity**
   Is the vulnerability actively being exploited in the wild? Security teams can use resources such as the **CISA Known Exploited Vulnerabilities (KEV) Catalog** to identify vulnerabilities known to be exploited.

2. **Asset Criticality**
   What system is affected? A vulnerability affecting a public-facing production server or authentication system may receive higher priority than the same vulnerability affecting an isolated test machine.

3. **Exposure**
   Is the vulnerable system accessible from the internet, an internal network, or only locally?

4. **Available Mitigations**
   Can the vulnerability be patched immediately, or are compensating controls such as firewall restrictions, segmentation, or configuration changes required?

### Example

> **Scenario:** A Medium-severity vulnerability with a CVSS score of 5.5 is being actively exploited and affects an internet-facing web server.

Although the CVSS score is **Medium**, the vulnerability should be treated as a higher operational priority because of its **active exploitation and exposure**.

This demonstrates an important SOC and vulnerability-management principle:

**CVSS indicates severity — risk context determines priority.**

# CVSS v3.1 — Common Base Metrics & Values

The **Common Vulnerability Scoring System (CVSS) v3.1** Base Score is calculated using metrics that describe how a vulnerability can be exploited and the potential impact of exploitation.

## Attack Metrics

### Attack Vector (AV)

Describes **how the attacker reaches the vulnerable component**.

* **Network (N)** — The vulnerability can be exploited remotely over a network, potentially across the internet.
* **Adjacent (A)** — The attacker must have access to the same shared or adjacent network.
* **Local (L)** — The attacker must have local access to the affected system.
* **Physical (P)** — The attacker must physically interact with the vulnerable device.

### Attack Complexity (AC)

Describes **how difficult the exploitation is once the attacker has access to the target**.

* **Low (L)** — No specialized conditions are required for exploitation.
* **High (H)** — Exploitation depends on specific conditions that are difficult for the attacker to control or reproduce.

### Privileges Required (PR)

Describes **the level of privileges an attacker must have before exploiting the vulnerability**.

* **None (N)** — No privileges or authentication are required.
* **Low (L)** — Basic or limited user privileges are required.
* **High (H)** — Significant privileges, such as administrator-level access, are required.

### User Interaction (UI)

Describes **whether a user must take some action for exploitation to succeed**.

* **None (N)** — The vulnerability can be exploited without user interaction.
* **Required (R)** — A user must perform an action, such as clicking a link or opening a file.

### Scope (S)

Describes **whether exploitation can impact resources outside the security authority of the vulnerable component**.

* **Unchanged (U)** — The impact remains within the security authority of the vulnerable component.
* **Changed (C)** — Exploitation can affect resources outside the security authority of the vulnerable component.

---

## Impact Metrics — CIA

The Impact metrics measure the effect of successful exploitation on the **CIA Triad**:

### Confidentiality (C)

Measures the impact on the **disclosure of information**.

* **None (N)** — No loss of confidentiality.
* **Low (L)** — Limited disclosure of information.
* **High (H)** — Significant or complete disclosure of information.

### Integrity (I)

Measures the impact on the **modification or destruction of information**.

* **None (N)** — No loss of integrity.
* **Low (L)** — Limited ability to modify information.
* **High (H)** — Significant or complete loss of integrity.

### Availability (A)

Measures the impact on the **availability of systems or resources**.

* **None (N)** — No impact on availability.
* **Low (L)** — Reduced availability or performance.
* **High (H)** — Significant or complete loss of availability.

> **Note:** CVSS v3.1 uses **N (None), L (Low), and H (High)** for the Confidentiality, Integrity, and Availability impact metrics. **Partial (P)** and **Complete (C)** are associated with older CVSS versions and should not be used for CVSS v3.1.

## Quick Reference

| Metric                       | Values     |
| ---------------------------- | ---------- |
| **Attack Vector (AV)**       | N, A, L, P |
| **Attack Complexity (AC)**   | L, H       |
| **Privileges Required (PR)** | N, L, H    |
| **User Interaction (UI)**    | N, R       |
| **Scope (S)**                | U, C       |
| **Confidentiality (C)**      | N, L, H    |
| **Integrity (I)**            | N, L, H    |
| **Availability (A)**         | N, L, H    |

### Example CVSS v3.1 Vector

`CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:H`

This describes a vulnerability that is **remotely exploitable**, has **low attack complexity**, requires **no privileges**, requires **user interaction**, has **unchanged scope**, and can have a **high impact on confidentiality, integrity, and availability**.

