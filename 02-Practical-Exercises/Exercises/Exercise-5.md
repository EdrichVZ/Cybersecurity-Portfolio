<img width="668" height="1117" alt="Exercise 5" src="https://github.com/user-attachments/assets/2cc67360-0c58-4e07-b5eb-8084e3967f31" />

# Task 1

1. **Email spoofing** (`billing@northgate-logistics-support.com`) and **sender email address mismatch** (`it-support@northgatelogistics.com`).

   * **SPF fail** — `198.51.100.203` isn't an authorized sender for `northgatelogistics.com`.

2. **Social engineering** — Subject: `Action Required: Password Expiring Today`, creating a sense of urgency.

3. **Impersonation** — The email is made to seem like a legitimate Northgate Logistics email.

4. **Typosquatted link** (`northgate-1ogistics-portal.com`) — a `"1"` has been swapped for an `"l"`.

5. **DKIM fail** — The cryptographic signature didn't validate, meaning the message wasn't actually signed by the legitimate domain key (or was altered in transit).

6. **DMARC fail (`p=reject`)** — Since both SPF and DKIM failed, DMARC's policy says this message should be rejected outright.

---

# Task 2

a.) The registry Run key addition = **Host-related IOC**

b.) The beaconing to `45.33.12.9` = **Network-related IOC**

c.) The auto-approved PAM elevation with no second approver = **Identity / Account-related IOC but in this case its Host-related**

---

# Task 3

**Authentication and Authorization controls have failed.**

If Authentication was implemented correctly, the attacker should not have been able to log in with just the credentials of user `m.chen`. For example, if MFA was implemented correctly, the attacker would have failed with just the credentials.

If Authorization was implemented correctly, the attacker wouldn't have gotten elevated access.

This failed because of an auto-approval for a PAM request and no second approver was required.

---

# Task 4

## Containment

* Revoke/reset credentials and elevated **Finance-Payroll-Admin** access immediately for user `m.chen` and isolate the workstation **FIN-WKS-08** from the network.
* Block the C2 IP (`45.33.12.9:8443`) and phishing domain/URL (`northgate-1ogistics-portal.com/login`) at the firewall/email gateway.

## Eradication

* Remove the malicious `excel.exe`.
* Delete `sync.exe`.
* Remove the `HKCU\...\Run\SyncHelper` registry entry.
* Scan **FIN-WKS-08** for any additional dropped files or persistence mechanisms.

