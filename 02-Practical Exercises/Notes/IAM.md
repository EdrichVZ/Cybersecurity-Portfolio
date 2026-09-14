# Identity and Access Management (IAM)

Identity and Access Management (IAM) is the process of managing **identities, authentication, authorization, and access** to systems and resources.

For the **CompTIA CySA+ CS0-003** exam, the main IAM concepts listed under Security Operations objective 1.1 are:

* Multifactor Authentication (MFA)
* Single Sign-On (SSO)
* Federation
* Privileged Access Management (PAM)
* Passwordless Authentication
* Cloud Access Security Broker (CASB)

---

## 1. Multifactor Authentication (MFA)

MFA requires **two or more different authentication factors** to verify a user's identity.

### Authentication Factors

| **Factor**             | **Example**                                |
| ---------------------- | ------------------------------------------ |
| **Something you know** | Password, PIN                              |
| **Something you have** | Security token, smartphone, smart card     |
| **Something you are**  | Fingerprint, facial recognition            |
| **Somewhere you are**  | Geographic location                        |
| **Something you do**   | Behavioral characteristics, typing pattern |

### Important Exam Point

Using two passwords does **not** constitute MFA because both are the same type of factor: **something you know**.

**Example:**

> Password + authenticator app code = MFA

MFA reduces the risk of account compromise if a password is stolen.

---

## 2. Single Sign-On (SSO)

SSO allows a user to authenticate once and access multiple applications or services without repeatedly entering credentials.

**Example:**

> A user signs into their corporate identity provider and can then access Microsoft 365, an internal application, and other authorized services without logging into each separately.

### Security Benefits

* Reduces the number of passwords users need to remember
* Centralizes authentication
* Makes account management easier
* Can integrate with MFA

### Security Risk

A compromised SSO account may provide access to multiple connected applications.

---

## 3. Federation

Federation allows identities and authentication to be **trusted across different organizations, domains, or services**.

Instead of each application maintaining its own user credentials, an organization can use an external **Identity Provider (IdP)** to authenticate users.

### Key Terms

* **Identity Provider (IdP)** — Authenticates the user
* **Service Provider (SP)** — Provides the application or service
* **Federated Identity** — An identity that can be trusted across different services or organizations

**Example:**

> An employee uses their organization's corporate identity to access a third-party cloud application.

### Federation vs SSO

They are related but not identical:

* **SSO** → One authentication session can provide access to multiple applications.
* **Federation** → Establishes trust between different identity domains or organizations.

---

## 4. Privileged Access Management (PAM)

PAM is used to control and protect accounts with **elevated or administrative privileges**.

Examples include:

* Domain administrators
* System administrators
* Database administrators
* Root accounts
* Service accounts with elevated privileges

### PAM Security Controls

* Restrict privileged accounts
* Monitor privileged activity
* Record administrative sessions
* Rotate privileged credentials
* Use temporary or just-in-time privileges
* Require additional authentication
* Remove unnecessary administrative access

### Principle of Least Privilege

Users should receive **only the permissions required to perform their job**.

**Example:**

> A help-desk technician may need permission to reset passwords but should not automatically have Domain Administrator privileges.

---

## 5. Passwordless Authentication

Passwordless authentication allows users to authenticate **without using a traditional password**.

Examples include:

* Biometrics
* Hardware security keys
* Passkeys
* Cryptographic authentication
* Device-based authentication

### Benefits

* Reduces password-related attacks
* Reduces phishing opportunities
* Eliminates password reuse
* Reduces credential-stuffing risk

### Important Exam Point

Passwordless does not simply mean "no authentication."

The user still needs to prove their identity using another authentication mechanism.

---

## 6. Cloud Access Security Broker (CASB)

A CASB is a security control positioned between an organization's users and cloud services.

It provides visibility and security controls for cloud applications and data.

### Common CASB Functions

| **Function**          | **Purpose**                                       |
| --------------------- | ------------------------------------------------- |
| **Visibility**        | Identify cloud applications and usage             |
| **Access Control**    | Control who can access cloud services             |
| **Data Security**     | Protect sensitive information                     |
| **Threat Protection** | Detect and respond to suspicious activity         |
| **Compliance**        | Help enforce organizational security requirements |

**Example:**

> A company uses a CASB to monitor employees accessing cloud storage and prevent sensitive company data from being uploaded to an unauthorized cloud service.

---

# Related IAM Concepts

Although the six concepts above are the **specific IAM technologies listed in CySA+ objective 1.1**, you should also understand these supporting concepts because they help you answer scenario-based questions.

| **Concept**                 | **What to Know**                                                                   |
| --------------------------- | ---------------------------------------------------------------------------------- |
| **Authentication**          | Verifying who a user or entity is                                                  |
| **Authorization**           | Determining what an authenticated user is allowed to access                        |
| **Accounting / Auditing**   | Recording and monitoring access and activity                                       |
| **Least Privilege**         | Give users only the permissions they require                                       |
| **RBAC**                    | Access based on a user's role                                                      |
| **ABAC**                    | Access based on attributes such as department, device, location, or security level |
| **Access Control**          | Controlling access to systems and resources                                        |
| **Identity Provider (IdP)** | System responsible for authenticating identities                                   |
| **Directory Services**      | Centralized storage and management of identities and resources                     |
| **Service Accounts**        | Accounts used by applications/services rather than normal users                    |
| **Privileged Accounts**     | Accounts with elevated permissions                                                 |
| **Account Lifecycle**       | Creating, modifying, disabling, and removing accounts                              |

---

# Authentication vs Authorization

This distinction is particularly important.

### Authentication

**"Who are you?"**

Verifies the identity of the user.

Example:

> Entering a username, password, and MFA code.

### Authorization

**"What are you allowed to do?"**

Determines what resources or actions the authenticated user can access.

Example:

> A user can access a shared folder but cannot modify its contents.

---

# AAA

A useful model for understanding access management is **AAA**:

| **AAA Component**  | **Meaning**                 |
| ------------------ | --------------------------- |
| **Authentication** | Verify identity             |
| **Authorization**  | Determine permitted actions |
| **Accounting**     | Record and monitor activity |

**Example:**

> A user authenticates with MFA → authorization determines which applications they can access → accounting records their activity.

---

# Common CySA+ Scenario Connections

IAM concepts can appear in security alerts and investigations.

| **Scenario**                                           | **Potential Security Concern**   |
| ------------------------------------------------------ | -------------------------------- |
| User logs in from an unusual location                  | Possible compromised credentials |
| Hundreds of failed login attempts                      | Brute-force attack               |
| New administrator account created                      | Possible privilege abuse         |
| Normal user receives administrator privileges          | Privilege escalation             |
| Disabled employee account logs in                      | Account compromise               |
| Service account interactively logs in                  | Potential misuse                 |
| Administrator account used from an unusual workstation | Possible credential compromise   |
| MFA disabled unexpectedly                              | Possible account takeover        |
| User accesses applications they normally don't use     | Possible compromised account     |
| Excessive privileges assigned to a user                | Least-privilege violation        |

---

# CySA+ IAM Quick Reference

| **Concept**         | **Remember**                                       |
| ------------------- | -------------------------------------------------- |
| **MFA**             | Multiple different authentication factors          |
| **SSO**             | One authentication session → multiple applications |
| **Federation**      | Trust between different identity domains/services  |
| **PAM**             | Protect and control privileged accounts            |
| **Passwordless**    | Authentication without traditional passwords       |
| **CASB**            | Security and visibility for cloud services         |
| **Authentication**  | Who are you?                                       |
| **Authorization**   | What can you access/do?                            |
| **Accounting**      | What did you do?                                   |
| **Least Privilege** | Minimum required permissions                       |
| **RBAC**            | Access based on roles                              |
| **ABAC**            | Access based on attributes                         |
| **IdP**             | Authenticates identities                           |

## Key Point

Unauthorized privilege change — An account gaining privileges it should not have. This can be an identity/account-related or behavioral indicator and may be observed through host or directory logs.

For example:

User added to Administrators group → Identity/account-related
Normal user suddenly receives Domain Admin privileges → Identity/account-related / behavioral
Attacker exploits a vulnerability to obtain SYSTEM privileges → Host-related / behavioral
New privileged account created unexpectedly → Identity/account-related

So don't write:

❌ "Unauthorized privileges are explicitly listed as a host-related indicator."

Write:

✅ "Unauthorized privilege changes are indicators of suspicious identity or account activity and can also indicate privilege escalation."

This is more accurate and safer for a CySA+ exam note, because CompTIA's exam objectives don't establish a rigid rule that every privilege change is specifically a "host-related IOC."

For **CySA+ CS0-003**, make sure you can recognize these IAM technologies in a scenario and understand their **security purpose, benefits, risks, and appropriate use**.

The six IAM items explicitly listed by CompTIA are:

> **MFA → SSO → Federation → PAM → Passwordless → CASB**
