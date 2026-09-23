# IOC Threat & Confidence Ratings

Threat and Confidence Ratings provide additional context when evaluating an **Indicator of Compromise (IOC)**.

They answer two different questions:

* **Threat Rating:** How serious or dangerous is this indicator?
* **Confidence Rating:** How certain are we that our assessment of the indicator is accurate?

These ratings should be considered separately. A highly dangerous indicator may have low confidence, while a less sophisticated indicator may have very high confidence.

---

# 1. Threat Rating

Threat Rating represents the **level of threat associated with an indicator**.

ThreatConnect uses a **0–5 scale**, with higher ratings representing more capable, determined, and advanced threats.

| Rating | Level             | Description                                                                                                  | Example                                                                                  |
| -----: | ----------------- | ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
|  **0** | ⚪ **Unknown**     | Not enough information to determine the threat level.                                                        | An unfamiliar IP has been observed, but there is insufficient information to assess it.  |
|  **1** | 🟡 **Suspicious** | Suspicious activity has been observed, but malicious activity has not been confirmed.                        | Users are repeatedly connecting to an unknown domain with no obvious legitimate purpose. |
|  **2** | 🟠 **Low**        | Represents a relatively unsophisticated or opportunistic threat.                                             | An IP performing broad internet scanning or opportunistic probing.                       |
|  **3** | 🔴 **Moderate**   | Represents a capable adversary conducting directed activity such as delivery, exploitation, or installation. | A malicious document specifically targeting employees in a particular department.        |
|  **4** | 🔴 **High**       | Represents an advanced, targeted, and persistent adversary.                                                  | A known C2 address associated with an ongoing targeted intrusion.                        |
|  **5** | 🚨 **Critical**   | Represents a highly capable and well-resourced adversary.                                                    | An indicator associated with a highly sophisticated intrusion and active compromise.     |

### Factors Used to Determine Threat Rating

When assigning a Threat Rating, consider:

* **Capability** — How skilled and well-resourced is the adversary?
* **Determination** — How focused and persistent is the adversary?
* **Progression** — How far has the activity progressed in the attack lifecycle?

For example:

```text
Scanning
   ↓
Exploitation
   ↓
Installation
   ↓
Command & Control
   ↓
Actions on Objective
```

An indicator associated with post-compromise **Command and Control (C2)** activity may represent a greater threat than an indicator associated with basic reconnaissance.

---

# 2. Confidence Rating

Confidence Rating represents **how confident an analyst is that the Threat Rating assessment is accurate**.

ThreatConnect uses a **0–100 scale**.

| Confidence | Level             | Meaning                                                                                       |
| ---------: | ----------------- | --------------------------------------------------------------------------------------------- |
|      **0** | ⚪ **Unassessed**  | No confidence assessment has been assigned.                                                   |
|      **1** | ❌ **Discredited** | The assessment has been confirmed to be inaccurate.                                           |
|   **2–29** | 🔴 **Improbable** | The assessment is possible but unlikely and is contradicted by other information.             |
|  **30–49** | 🟠 **Doubtful**   | The assessment is possible but not the most logical conclusion and lacks supporting evidence. |
|  **50–69** | 🟡 **Possible**   | The assessment is reasonably logical but only partially supported by available information.   |
|  **70–89** | 🟢 **Probable**   | The assessment is logical, plausible, and consistent with other information.                  |
| **90–100** | 🟢 **Confirmed**  | The assessment has been confirmed through independent sources or direct analysis.             |

---

# 3. Threat vs Confidence

These ratings should **not be confused with each other**.

### High Threat + Low Confidence

```text
Threat:     5 / Critical
Confidence: 20 / Improbable
```

The indicator could represent a very serious threat, but there is currently little evidence supporting that assessment.

**Action:** Investigate and gather additional intelligence.

---

### Low Threat + High Confidence

```text
Threat:     2 / Low
Confidence: 95 / Confirmed
```

There is strong evidence that the indicator is associated with a threat, but the threat itself is relatively unsophisticated.

**Action:** Apply an appropriate response based on the actual risk.

---

### High Threat + High Confidence

```text
Threat:     5 / Critical
Confidence: 95 / Confirmed
```

The indicator represents a serious threat and there is strong evidence supporting the assessment.

**Action:** Prioritize investigation, containment, and response.

---

# 4. Example IOC Assessment

### Indicator

```text
IP Address: 203.0.113.50
```

Investigation shows that the IP is associated with a known C2 infrastructure used during a targeted intrusion.

### Assessment

| Attribute             | Rating                                                                   |
| --------------------- | ------------------------------------------------------------------------ |
| **Indicator**         | `203.0.113.50`                                                           |
| **Type**              | IP Address                                                               |
| **Threat Rating**     | **4 — High**                                                             |
| **Confidence Rating** | **90 — Confirmed**                                                       |
| **Reason**            | Associated with known C2 activity and confirmed through multiple sources |

This provides much more context than simply labeling the IP as **"malicious."**

---

# 5. Key Factors for Confidence

When deciding how confident you are in an assessment, ask:

### Has it been confirmed?

Has the indicator been verified through:

* Direct analysis?
* Multiple independent sources?
* Reliable threat intelligence?
* SIEM or EDR evidence?

### Is the assessment logical?

Does the available evidence actually support the conclusion?

### Does other information agree?

Does the indicator correlate with:

* Other IOCs?
* Known attack techniques?
* Security alerts?
* Malware analysis?
* Historical threat intelligence?

ThreatConnect recommends considering **confirmation, plausibility, and consistency** when assigning confidence.

---

# Quick Reference

| Concept               | Question                                | Scale     |
| --------------------- | --------------------------------------- | --------- |
| **Threat Rating**     | How dangerous is the indicator?         | **0–5**   |
| **Confidence Rating** | How confident are we in our assessment? | **0–100** |

### Remember

> **Threat = Severity of the threat**
> **Confidence = Strength of the evidence**

A good SOC analyst should consider **both** before deciding how an IOC should be handled.
