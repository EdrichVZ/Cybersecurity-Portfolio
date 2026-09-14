<img width="668" height="1117" alt="Exercise 5" src="https://github.com/user-attachments/assets/2cc67360-0c58-4e07-b5eb-8084e3967f31" />

# Task 1

### Phishing Email Indicators

1. **Email Spoofing / Sender Mismatch**

   * Displayed sender: `billing@northgate-logistics-support.com`
   * Sender address: `it-support@northgatelogistics.com`
   * **SPF failed** — `198.51.100.203` is not an authorized sender for `northgatelogistics.com`.

2. **Social Engineering**

   * Subject: `Action Required: Password Expiring Today`
   * The message creates a sense of urgency to pressure the recipient into taking immediate action.

3. **Impersonation**

   * The email is designed to appear as though it originates from a legitimate Northgate Logistics source.

4. **Typosquatted Link**

   * `northgate-1ogistics-portal.com`
   * The domain uses the number **`1`** in place of the letter **`l`** to make the domain appear legitimate.

5. **DKIM Failure**

   * The DKIM cryptographic signature failed validation, indicating that the message was not successfully authenticated using the legitimate domain key or that the message may have been modified.

6. **DMARC Failure**

   * DMARC failed with a policy of **`p=reject`**.
   * Because both SPF and DKIM failed, the domain's DMARC policy indicates that the message should be rejected.

---

# Task 2

### IOC Classification

**a.) Registry Run Key Addition**

`HKCU\...\Run\SyncHelper`

→ **Host-related IOC**

The registry modification creates a persistence mechanism that can cause a malicious program to execute when the user logs in.

**b.) C2 Beaconing**

`45.33.12.9:8443`

→ **Network-related IOC**

Repeated communication with an external IP address over TCP/8443 may indicate command-and-control (C2) communication.

**c.) Unauthorized PAM Elevation**

Auto-approved PAM elevation with no second approver.

→ **Identity / Account-related and Behavioral Indicator**

The issue involves unauthorized elevation of an account's privileges and a failure in the privileged-access approval process.

---

# Task 3

### Authentication and Authorization Control Failure

Both **authentication and authorization controls** were compromised or insufficient in this scenario.

**Authentication failure:**

The attacker was able to authenticate using the credentials of user `m.chen`. If stronger authentication controls such as **MFA** had been correctly implemented and enforced, possession of the user's credentials alone should not have been sufficient to authenticate successfully.

**Authorization failure:**

After gaining access to the account, the attacker was able to obtain
