<img width="1000" height="446" alt="Alert 3" src="https://github.com/user-attachments/assets/ec59896b-7b01-461a-b5e4-33845dc9f2cf" />

From the alert details we can see that the resource where the activity occurred is in this case `http://web.trywinme.thm/`, which is the company website hosted on the web server. Let's check the **Suspicious IP:** `171.251.232.40` across a threat intelligence platform like AbuseIPDB.

<img width="769" height="655" alt="A3-1" src="https://github.com/user-attachments/assets/5325e41b-7f81-4415-8e69-f7878766fc48" />

As expected, the **Suspicious IP:** `171.251.232.40` is flagged as malicious. Let's inspect the logs to determine which specific activity was carried out by this IP address.

<img width="2560" height="1392" alt="A3-2" src="https://github.com/user-attachments/assets/daffa3f2-da40-4dfc-b680-f028361ad1cc" />

We immediately notice a lot of requests associated with this IP. Also, the User-Agent is set to `Hydra`, a popular tool to perform brute force attempts—in this case against the `wp-login.php` page. This is already a clear malicious indicator; however, the alert is about a web shell, so let's exclude `Hydra` as the User-Agent and view the results.

<img width="2560" height="1392" alt="A3-3" src="https://github.com/user-attachments/assets/d0945e15-b55e-48e6-88a6-854e83753f34" />

We can see a POST request was observed for `admin-ajax.php` with a referrer pointing to `theme-editor.php?file=b374k.php`. This is highly suspicious because the theme editor shouldn't reference arbitrary `.php` files. The presence of `file=b374k.php` strongly suggests that the attacker may have uploaded or is interacting with a web shell. Let's investigate into logs related to `b374k.php`.

<img width="2560" height="1392" alt="A3-4" src="https://github.com/user-attachments/assets/56d4441f-195d-4513-8f18-99210d554378" />

We see that the threat actor successfully gained access to a possible web shell file, `b374k.php`, and then began executing activity through it—specifically, we observed four successful POST requests.

Unfortunately, the logs do not show how the attacker initially uploaded the web shell to the server. However, we identified malicious activity originating from a Vietnamese IP address `171.251.232.40` targeting the web server. The suspicious activity began with a brute force attack using Hydra against `wp-login.php`, followed by clear evidence of web shell activity involving `b374k.php`.

> **Verdict:** True Positive — Escalated to L2 Analyst.

### Recommended Remediation

**1. Isolate the compromised web server**
* Restrict or isolate the affected web server to prevent further attacker activity and lateral movement.
* Preserve relevant logs and forensic evidence before making major changes.
* Block the identified malicious IP `171.251.232.40` at the appropriate firewall/WAF layer, while recognizing that IP blocking alone is not sufficient.

**2. Remove the web shell**
* Quarantine and analyse `b374k.php` before deletion.
* Remove the malicious web shell and search the web server for additional unauthorized PHP files or modified files.
* Check WordPress core files, themes, plugins, and uploads for unauthorized modifications.
* Compare files against known-good versions where possible.

**3. Investigate the compromised WordPress account**
* Determine whether the attacker gained access through the WordPress login page using the observed Hydra brute-force activity.
* Review WordPress authentication logs to identify successful logins following the brute-force attempts.
* Disable or reset credentials for any compromised accounts.
* Enable MFA for administrative WordPress accounts where possible.
* Review existing administrator accounts and remove any unauthorized accounts.

**4. Investigate the initial access vector**
* Determine how `b374k.php` was uploaded or created on the server.
* Investigate vulnerable or outdated WordPress plugins, themes, WordPress itself, or stolen administrator credentials.
* Review web server, WordPress, and file-system logs around the time the web shell first appeared.
* Determine whether the attacker exploited a vulnerability or authenticated legitimately using compromised credentials.

**5. Hunt for additional attacker activity**
* Search Splunk for `171.251.232.40`, `b374k.php`, `admin-ajax.php`, `theme-editor.php`, and related indicators.
* Investigate the four POST requests made through `b374k.php`.
* Look for commands executed through the web shell, file downloads, additional payloads, outbound connections, and attempts to establish persistence.
* Search other web servers for the same indicators to determine whether the attack was broader than one host.

**6. Harden the web application**
* Update WordPress, plugins, themes, and the underlying operating system.
* Remove unnecessary or vulnerable plugins and themes.
* Disable the WordPress theme/plugin editor if it is not required.
* Implement a WAF and appropriate rate limiting to reduce automated brute-force attacks.
* Restrict administrative interfaces such as `wp-login.php` where practical.
* Ensure the web server process has the minimum permissions required and cannot unnecessarily modify application files.

**7. Recovery and monitoring**
* If the server's integrity cannot be established, rebuild it from a known-good image or backup rather than relying solely on file deletion.
* Rotate potentially compromised credentials, API keys, and other secrets stored on the server.
* Continue monitoring for requests to `b374k.php` and other newly created PHP files.
* Create or tune SIEM detections for repeated authentication failures followed by successful authentication and suspicious PHP file access.
