<img width="836" height="1163" alt="Exercise 2" src="https://github.com/user-attachments/assets/ea6eb298-aec2-48d5-8369-6122933fe55a" />

# Vulnerability Prioritization

## Task 1

1. **V-1** because it's **Critical CVSS**, no authentication is required, and a public exploit code exists. It's also on the **CISA KEV Catalog**, meaning it's actively being exploited in the wild, not just theoretically.

2. **V-2** because **SMBv1** is running on an internal file server and has a **High CVSS score (8.1)**. SMBv1 is a well-known attack path. Although there is no public exploit yet, a compromised internal host reaching this server could provide a real **lateral movement** path.

3. **V-4** — Stored **XSS** with a PoC exploit that touches **PII**. It has a lower CVSS score (**6.1**) than V-5, but it has an actual **proof-of-concept exploit** and a real data exposure risk, which moves it above V-5.

4. **V-5** — Missing security headers / **clickjacking** on your critical public server. Yes, it's on your most critical asset, but the CVSS is low (**4.3**), it requires **user interaction**, and there's no working exploit. It still needs fixing, just not urgently.

5. **V-3** — Lowest priority despite the **7.5 CVSS score** because it's isolated.

## Task 2

**V-1** because it's listed in the **CISA KEV Catalog**, meaning there is confirmed active exploitation in the real world, not just theoretically.

Two vulnerabilities can have identical CVSS scores, but if one is in **KEV**, it jumps to the top of the remediation queue because KEV membership means threat actors are already using it against real targets, not just that it's theoretically possible.

> **CVSS tells you severity; KEV tells you urgency.**

## Task 3

**V-3** has no internet access, no sensitive data, and is on an isolated network. The effective risk is therefore much lower than the **7.5 CVSS score** suggests.

We would still patch it eventually because the system is technically vulnerable, but it drops to the bottom of this week's queue.

> **Raw CVSS score is a starting point, not the final verdict.**

Always adjust vulnerability priority based on **exploitability**, **exploit availability / KEV status**, and **real environmental exposure**.
