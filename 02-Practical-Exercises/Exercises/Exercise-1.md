<img width="947" height="922" alt="Exercise 1" src="https://github.com/user-attachments/assets/cb72ca4b-2ca0-438c-9c1b-e49678c42acc" />

# Task 1 — Brute Force Attack

A brute force attack based on **3 `Failed password` attempts** against the **`admin`** account and **2 attempts** against the **`root`** account from the IP **`203.0.113.44`** via port **`51325`**. These failed attempts were then followed by an **`Accepted password`** for the `admin` account from the IP **`203.0.113.44`** via port **`51402`**, suggesting the attack was successful and resulting in access to **`web-prod-03`**.

# Task 2 — Privilege Abuse & Persistence

After the successful login with an **admin account**, the attacker ran **`sudo`** to execute commands as `root` and created a new system user called **`svc-backup`** with a home directory (`-m`) using the command:

```bash
/usr/bin/useradd -m svc-backup
```

This represents **privilege abuse**.

The attacker then set a password using:

```bash
/usr/bin/passwd svc-backup
```

for the newly created **`svc-backup`** account so it could potentially be accessed later.

Then, using the command:

```bash
/bin/crontab -e -u svc-backup
```

the attacker edited (`-e`) the scheduled tasks (**crontab**) specifically for the new user (`-u`) **`svc-backup`** account. This was likely intended to place a reverse shell or malware execution script to maintain long-term access.

This represents a **Persistence** technique, potentially designed to disguise a backdoor account as a legitimate system service account.

# Task 3 — Response

We are currently in the **Detection Phase**. The next step would be to move into **Containment**. This could be achieved by revoking or resetting the compromised **admin credentials** and isolating the host **`web-prod-03`** if required.

We would then move into **Eradication** by removing the **`svc-backup`** account and its associated **cron job (scheduled task)**.
