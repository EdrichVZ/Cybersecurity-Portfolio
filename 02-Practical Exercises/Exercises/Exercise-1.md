<img width="947" height="922" alt="Exercise 1" src="https://github.com/user-attachments/assets/cb72ca4b-2ca0-438c-9c1b-e49678c42acc" />

Task 1: 
A Brute force attack based on 3 'Failed password' attempts against the 'admin' and 2 attempts against the 'root' account from the IP 203.0.113.44 via port 51325.
These failed attempts were then followed by a 'Accepted password' for the admin account from the IP 203.0.113.44 via Port 51402 suggesting the attack was successful and resulting in access to web-prod-03

Task 2:
After the succesful login with an admin account the attacker created a new system user called 'svc-backup' with a home directory (-m) (COMMAND=/usr/bin/useradd -m svc-backup) (privelige abuse).
The attacker then set a password (COMMAND=/usr/bin/passwd svc-backup) for the newly created 'svc-backup' account so it can be accessed later.
Then using the command (COMMAND=/bin/crontab -e -u svc-backup) the attacker edited (-e) the scheduled tasks (crontab) specifically for the new user (-u) svc-backup account. probably to place reverse shell or malware execution scripts to maintian long-term access.
This is the Persistence technique designed to disguise a backdoor account as a legitimate system service account.
