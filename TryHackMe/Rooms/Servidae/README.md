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



Most of destination IPs are in same network/local host, so IP: ```**84.237.252.156**``` - stands out. 




<img width="273" height="525" alt="Screenshot (89)" src="https://github.com/user-attachments/assets/f382ebb0-e01a-4bf0-ba6b-eec730ad6198" />


**Use an IP address lookup tool (such as iplocation.io). What country does this IP address originate from?**




IP Lookup, country in ```**Latvia**```




<img width="818" height="321" alt="Screenshot (90)" src="https://github.com/user-attachments/assets/fcf0166b-2199-4d9b-9656-d4491e14d9f4" />




**Which process name is running the most frequently on the compromised workstation?**


Let's just go through the top 10 in the *process.name*


<img width="267" height="498" alt="Screenshot (91)" src="https://github.com/user-attachments/assets/c0979ab6-92c9-429e-8990-1f0f77870d39" />



```**curl.exe**```


During this, I learned that you could filter for the pure existence of a field name? Wow. I wish I learned that sooner.



*Filter for field present* - along with that, I might've found more by accident (wrote it down, but won't dig right now til I get there)



<img width="840" height="537" alt="Screenshot (92)" src="https://github.com/user-attachments/assets/b43a4933-1934-4d34-9997-b6f71fb83222" />





Ensure its the correct user. From here, I can guess that SYSTEM was used in some way (probably maliciously)




<img width="850" height="442" alt="Screenshot (93)" src="https://github.com/user-attachments/assets/452e556c-989e-468c-b0cc-781eba594f5f" />




**What was the process ID (PID) of the potentially malicious PowerShell script?**


Place the time in ascending order, and add the field name associated

```**6712**```

<img width="1161" height="294" alt="Screenshot (95)" src="https://github.com/user-attachments/assets/13a66802-8ad3-46bc-8b04-6a8b181082eb" />



Here, (if we back track to the phishing email) we learn that bill downloaded the file extension-spoofed powershell script - essentially a RAT

<img width="535" height="318" alt="Screenshot (94)" src="https://github.com/user-attachments/assets/ee1e031c-423c-4798-910d-fa757aa5720f" />





**What was the parent process name of the process that spawned powershell.exe?**

Simply add another field name

```**explorer.exe**```

<img width="850" height="333" alt="Screenshot (96)" src="https://github.com/user-attachments/assets/6459ab4c-f71a-4711-97f5-56d6314d8089" />







**What is the domain name of the attacker’s server hosting the winPEAS executable?**


Since there's domain and server involved, it can be assumed that either HTTP requests or Invoke-WebRequest is occurring. 


```**evilparrot.thm**```

This executable is from that web server, which reveals possible privilege escalation paths


<img width="844" height="421" alt="Screenshot (97)" src="https://github.com/user-attachments/assets/262e5c75-72b6-47b2-a64f-9a33e7b3ecf4" />



**What is the full path of the HKEY_LOCAL_MACHINE registry entry that was queried?**

The question made it easy to search for this, but also there's not many logs to go through.


The key is **reg query** that can be recycled within queries to search for registry changes.


```**HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\Installer**```

The executable found this privilege escalation path:
```AlwaysInstallElevated``` 


can install programs or updates with elevated privileges, and attacker takes advantage of that with MSI


<img width="841" height="624" alt="Screenshot (98)" src="https://github.com/user-attachments/assets/b106e5f5-9f4a-4d59-b0c6-28e160b02077" />







**What is the flag sent via cURL requests to the evilparrot.thm server?**


```THM{C4N_Y0U_h34r_m3}```


<img width="857" height="333" alt="Screenshot (100)" src="https://github.com/user-attachments/assets/b5a03221-1e3e-4903-96cf-7eba6832e0df" />














**What was the password that the attacker used to access Bill’s user account on the internal payroll website?**




```**Password123!**```

Pfft, weak password


But seriously, after discovery, execution, C2, attacker got a successful login to an internal server WHICH can be discovered with recon scans. It made me think of my home lab using nmap, but you definitely would not want to download nmap on a victim machine or they can be alerted. In this case, the attacker just pinged the network using ICMP.


<img width="874" height="551" alt="Screenshot (102)" src="https://github.com/user-attachments/assets/1a760fa5-a556-4819-b964-61e1e98d0a09" />




**What flag was included within the HTTP requests during the attacker’s successful logins?**

```**THM{1m_1N_Y0ur_P4YR0LL}**```


**What was the session cookie value that the attacker included in the cURL request at 18:58:08.001?**

```**dt5qhq423goknmq269rg1tal1a**```


**What is the name of the sensitive file that the attacker downloaded?**


Once you get in, establish a way to get back in (execution - persistence), have connections back to something you control (c2), escalate privileges to do further execution, persistence and discovery, you can find a source to capture and finish your mission (which you would likely have defined the mission goal before starting this). In this case, it's data exfiltration of sensitive data, which there's multiple ways to go about it, but in this case, it was with *curl*


<img width="1881" height="537" alt="Screenshot (105)" src="https://github.com/user-attachments/assets/0c782666-dfc1-44f0-979c-836a88e7e4b1" />


