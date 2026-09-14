<img width="836" height="1163" alt="Exercise 2" src="https://github.com/user-attachments/assets/ea6eb298-aec2-48d5-8369-6122933fe55a" />

Task 1:
1. V-1 because its critical CVSS, no authentication required and a public exploit code exists. It's also on the CISA's list meaning its actively being exploited in the wild not just theoritcally.
2. V-2 because SMBv1 on an internal file server and high CVSS (8.1), SMBv1 is a well-known attack path. Although no public exploit yet but a compromised internal host reaching this server could be a real lateral-movement path.
3. V-4 Stores XSS with a PoC exploit and it touches **PII.** Lower CVSS (6.1) than V-5, but it has an actual proof-of-concept exploit and real data exposure risk — moves above V-5.
4. V-5 Missing headers/clickjacking on your critical public server. Yes, it's on your most critical asset, but the CVSS is low (4.3), it requires user interaction, and there's no working exploit. Still needs fixing, just not urgently.
5. V-3 — lowest priority despite the 7.5 score because its isolated.

Task 2:
V-1 because its a CISA KEV = confirmed active exploitation in the real world, not just theoritcally. Two vulnerabilities can have identical CVSS scores, but if one is in KEV, it jumps to the top of every remediation queue — because KEV membership means threat actors are already using it against real targets, not just that it's theoretically possible. CVSS tells you severity; KEV tells you urgency.

Task 3:
V-3 has no internet access, no sensitive data and is on a isolated network. The effective risk much lower than 7.5 suggests. We would still patch it eventually (it's technically vulnerable), but it drops to the bottom of this week's queue. Raw CVSS score is a starting point, not the final verdict, always adjust for exploitability, exploit availability/KEV status, and real environmental exposure.
