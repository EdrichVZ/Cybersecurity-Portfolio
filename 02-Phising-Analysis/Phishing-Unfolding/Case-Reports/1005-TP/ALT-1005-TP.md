# Alert 1005 Case Report - Suspicious Email Attachment

## 1. Summary
* **Classification:** True Positive
* **Severity:** High
* **Escalation Required:** Yes
* **Timestamp:** `09/05/2026 13:03:12.688`
* **Summary:** A phishing email originating from `john@hatmakereurope.xyz` was delivered to `michael.ascot@tryhatme.com` carrying a malicious zip archive. The payload relies on urgency and file extension masking (`.pdf.lnk`) to trick the target into executing malicious code.

---

## 2. Affected Entities & Email Details
* **Sender:** `john@hatmakereurope.xyz`
* **Recipient:** `michael.ascot@tryhatme.com`
* **Subject Line:** `FINAL NOTICE: Overdue Payment - Account Suspension Imminent`

---

## 3. Triage & Analysis

**True Positive Justification:**
The email exhibits social engineering tactics by creating a sense of urgency (imminent account suspension) to coerce the user into acting without thinking. Additionally, the attached ZIP file contains a hidden `.lnk` shortcut file masked as a PDF (`invioce.pdf.lnk`) designed to execute arbitrary commands upon interaction.

**Escalation Justification:**
Escalation is required due to the presence of confirmed malicious payload artifacts. The external domain `hatmakereurope.xyz` is highly suspicious, and both the archive name (`Febrary`) and file name (`invioce`) contain spelling anomalies typical of phishing campaigns. Automated security scans verified that the `.lnk` file is malicious and poses a direct execution risk.

---

## 4. Recommended Remediation Actions
1. **Mailbox Isolation:** Quarantine `ImportantInvoice-Febrary.zip` and `invioce.pdf.lnk` directly from the recipient's inbox.
2. **Gateway Blocks:** Block sender `john@hatmakereurope.xyz` and the root domain `hatmakereurope.xyz` at the secure email gateway.
3. **Filter Tuning:** Update email security rules to automatically flag incoming messages containing double file extensions (e.g., `.pdf.lnk`) or suspicious `.zip` attachments from untrusted sender domains.
4. **Endpoint Verification:** Run a full AV/EDR scan on Michael Ascot's endpoint (`win-3450`), and inspect process launch logs to confirm `invioce.pdf.lnk` was not executed.
5. **User Security Awareness:** Enroll Michael Ascot in targeted phishing awareness training regarding fake invoice scams and attachment safety.

---

## 5. Indicators of Compromise (IOCs)
* **Sender Address:** `john@hatmakereurope.xyz`
* **Malicious Domain:** `hatmakereurope.xyz`
* **Archive File:** `ImportantInvoice-Febrary.zip`
* **Malicious Payload:** `invioce.pdf.lnk`
* **Attack Tactics:** Social Engineering (Urgency, account suspension threats, double extension file masking)

<img width="2560" height="1392" alt="ALT-1005" src="https://github.com/user-attachments/assets/e6de0a14-c6e0-4813-9614-f0c4bf71870a" />
<img width="2560" height="1392" alt="ALT-1005-Attachment" src="https://github.com/user-attachments/assets/7b8c2382-9dd6-40e7-b530-495169cb4be1" />
