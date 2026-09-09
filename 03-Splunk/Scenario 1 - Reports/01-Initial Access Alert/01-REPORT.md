<img width="2560" height="1392" alt="A1-2" src="https://github.com/user-attachments/assets/485b9dd1-e6fb-4e77-bf17-34c261ff4ce8" /><img width="1024" height="442" alt="Alert 1" src="https://github.com/user-attachments/assets/41ab8657-faa8-4e84-8b4f-d579eb16e414" />

# Incident Investigation Report: Successful Brute-Force Attack

## Summary
* **Severity:** High
* **Classification:** True Positive
* **Status:** Escalated to L2 Analyst
* **Target User:** `john.smith`
* **Target Host:** `tryhackme-2404`
* **Source IP:** `10.10.242.248` (Internal Network)

---

## Analysis

From the alert details we can see that the **Source IP:** `10.10.242.248` is a local IP address and the activity time is during normal working hours, suggesting the alleged attacker might possibly be already inside the corporation network and might have successfully compromised the VPN or gained access some other way. 

Let’s investigate how many events there are for both successful and failed login attempts as well as invalid users associated with the **Source IP:** `10.10.242.248`.

<img width="2560" height="1392" alt="A1-1" src="https://github.com/user-attachments/assets/c65bc40c-ade8-4975-a3e4-3f85a1fb7855" />

We can see that **543 events** were associated with the source IP, which is quite a lot. Let’s check how many events are for invalid users and invalid password attempts respectively.

<img width="2560" height="1392" alt="A1-2" src="https://github.com/user-attachments/assets/f3a5fe98-6e96-4534-a3c7-15caa8a308f6" />
<img width="2560" height="1392" alt="A1-3" src="https://github.com/user-attachments/assets/709f4d72-50b8-4595-ac12-caab9b1969ee" />

![Uploading A1-3.png…]()
Notice that there were **508 failed password events** and **40 invalid users events**, which is highly suspicious. However, to confirm if a brute force attack has taken place, let’s look at the total login attempts for each user next.

<img width="2560" height="1392" alt="A1-4" src="https://github.com/user-attachments/assets/78425a61-196c-4049-9c1d-1fc0153dd0fa" />


We can now confirm that a brute force attack has taken place against the user `john.smith` with **504 login attempts**, which is highly irregular. We now need to confirm if the attack was successful or not by looking if there were any successful logins for user `john.smith`.

<img width="2560" height="1392" alt="A1-5" src="https://github.com/user-attachments/assets/4df82176-f73f-4969-b356-71af82594e39" />


Notice there are only successful logins for the user `john.smith`, confirming that a brute force attack has taken place and was successful. 

> **Verdict:** True Positive — Escalated to L2 Analyst.

### Recommended Remediation

**1. Contain the compromised account**
* Immediately disable or lock the `john.smith` account.
* Force a password reset before re-enabling the account.
* Revoke active sessions, SSH keys, VPN sessions, tokens, and other authentication credentials associated with the account.
* Investigate whether the same credentials were used on other systems.

**2. Contain the compromised host**
* Isolate `tryhackme-2404` from the network while preserving it for forensic investigation.
* Block or investigate the source IP `10.10.242.248`.
* Determine whether the IP belongs to a legitimate internal system, VPN user, compromised workstation, or another potentially compromised asset.

**3. Investigate the successful compromise**
* Review all activity performed by `john.smith` after the successful login.
* Investigate the root privilege escalation and determine how it was achieved.
* Review executed commands, processes, files modified, network connections, and authentication activity.
* Investigate the creation and activity of the `System-utm` account and remove it if confirmed malicious.

**4. Hunt for additional compromise**
* Search the SIEM for `10.10.242.248` across other hosts.
* Search for `john.smith`, `System-utm`, and other indicators associated with the incident.
* Check whether the same source IP attempted authentication against additional systems.
* Look for lateral movement, persistence, data access, or additional privilege escalation.

**5. Eradicate the attacker**
* Remove unauthorized accounts, SSH keys, scheduled tasks, services, scripts, or other persistence mechanisms.
* Patch the vulnerability that allowed privilege escalation, if applicable.
* Rebuild or restore the host if its integrity cannot be confidently established.

**6. Recovery and monitoring**
* Restore the affected system to a known-good state.
* Reset credentials that may have been exposed.
* Increase monitoring for the affected host and accounts.
* Create or tune Splunk detection rules for high-volume authentication failures and successful authentication following brute-force activity.
