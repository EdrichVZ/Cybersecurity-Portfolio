<img width="784" height="1134" alt="Exercise 4" src="https://github.com/user-attachments/assets/a42c47d7-c90a-4019-bb7e-135f689e086c" />

Task 1:
•	Alert 1 shows j.alvares established and successfully authenticated into the internal network outside normal business hours. Which is Anomalous user account activity and unauthorised access which is Host related IOC.
•	Alert 2 shows Outlook spawning a PowerShell process which isn’t normal. This Powershell executed a Base64 coded obfuscated command that added a new registry value to change a Windows startup setting, (this makes malware or whatever restart everytime the machine gets turned on). The script also droppen a file called syshelp.dll into a public folder and ran it which started rundll32.exe (This is a HOST-based IOC).
•	Alert 3 shows RAD-WKS-22 is actively beaconing to an external source every 30s its then shows RAD-WKS-22 connecting to an internal file server and transferring 4.2 GB of data to an internal file server (FILE-SRV-03) using SMB (staging a possible data-exfiltration). This is indicating of C2 Command and Control which is Network-related IOC.


Task 2:
Authentication, if Multi-factor Authentication was implemented correctly the attacker would not have been able to login. If say an Inheritance factor such as biometrics was implemented the attacker could not have succeeded with stolen credentials alone.

Task 3:
It’s a scale used by analysts to determine how confident they are that a threat is actually dangerous based on accurate, logical and external data. A Probable score is between 70 -89 and it means that the threat is highly plausible as it is supported by available information. Knowing this I would probably block 198.51.100.7 from the corporate network

Task 4:
The FILE-SRV-03 has a CVSS of 9.1 which means the vulnerability need to remediated immediately, a score as high as this allows complete system takeover or remote code execution. In this incident it seems to be the threat actor has probably already took over the file server FILE-SRV-03 and is preparing a large scale data exfiltration. Knowing this both RAD-WKS-22 and FILE-SRV-03 needs to be isolated immediately to cutoff the C2 Command and Control communication. After isolation the eradication of malicious software needs to happen and patches need to be applied so that the vulnerability gets remediated.
