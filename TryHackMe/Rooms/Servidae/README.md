## Context Info For Starting Investigation on Splunk
Note: this room is about reviewing an incident that happened

1. Bill Smith
2. Senior Finance Executive
3. got an email from unknown sender with PDF file attachment requiring action
4. Bill downloaded and opened it

5. Suspicious network activity
6. Unauthorized commands executed on workstation
7. Possible remote access
8. SERVIDAE-BOB-FIN-04
9. May 11 2023 @ 18:45:00 - May 11 2023 @ 19:01:00




*920 logs in 16 minutes*




<img width="1151" height="321" alt="Screenshot (88)" src="https://github.com/user-attachments/assets/f1489d2d-ad9a-48fc-9730-95d06cbc0fba" />



**Look at the Top values under the destination.ip field. Which IP address stands out?**



Most of destination IPs are in same network/local host, so IP: **84.237.252.156** - stands out. 




<img width="273" height="525" alt="Screenshot (89)" src="https://github.com/user-attachments/assets/f382ebb0-e01a-4bf0-ba6b-eec730ad6198" />


**Use an IP address lookup tool (such as iplocation.io). What country does this IP address originate from?**




IP Lookup, country in **Latvia**




<img width="818" height="321" alt="Screenshot (90)" src="https://github.com/user-attachments/assets/fcf0166b-2199-4d9b-9656-d4491e14d9f4" />




**Which process name is running the most frequently on the compromised workstation?**


Let's just go through the top 10 in the *process.name*


<img width="267" height="498" alt="Screenshot (91)" src="https://github.com/user-attachments/assets/c0979ab6-92c9-429e-8990-1f0f77870d39" />



**curl.exe**


During this, I learned that you could filter for the pure existence of a field name? Wow I wish I learned that sooner.



<img width="1144" height="79" alt="Filter Exists" src="https://github.com/user-attachments/assets/96b9a814-8cb7-4e52-b2be-a9ad9fdd72f0" />




**What was the process ID (PID) of the potentially malicious PowerShell script?**




**What was the parent process name of the process that spawned powershell.exe?**




**What is the domain name of the attacker’s server hosting the winPEAS executable?**




**What is the full path of the HKEY_LOCAL_MACHINE registry entry that was queried?**




**What is the name of the malicious .msi file?**




**What is the name of the user account that the attacker created to maintain privileged access?**




**What is the flag sent via cURL requests to the evilparrot.thm server?**




**What is the name of the registry value that the attacker added?**




**What was the password that the attacker used to access Bill’s user account on the internal payroll website?**




**What flag was included within the HTTP requests during the attacker’s successful logins?**




**What was the session cookie value that the attacker included in the cURL request at 18:58:08.001?**




**What is the name of the sensitive file that the attacker downloaded?**

