
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
