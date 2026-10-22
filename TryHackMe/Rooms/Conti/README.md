# SOC LVL 2 Capstone Challenge

## Don't attempt to visit or interact with URLs in Ransom Note

## Context

* Splunk!
* Employees reported that they can't log into Outlook
* Exchange System Admin reports he can't log in to Exchange Admin Center
* *Ransomware notes* on the Exchange server!
* 2 screenshots of error messages
1. Server Error in ```/ecp``` application - resource cannot be found - HTTP 404 - Requested URL: /ecp/default.aspx
2. Screenshot will be more efficient

<img width="1853" height="446" alt="image" src="https://github.com/user-attachments/assets/89ed3751-c98e-4050-8841-3c7dde2ab5fc" />







# *Investigate the situation!*

*I'm still a bit iffy looking through Microsoft applications, but it shouldn't be too difficult*

Let's begin

<img width="481" height="92" alt="Screenshot 2026-10-22 114439" src="https://github.com/user-attachments/assets/e5e3b9d5-4cf8-439e-a412-66ac7de71b69" />




## Can you identify the location of the ransomware?

Always start with context information, and move from there -

Looking through the source outputs, I already found the readme.txt -

<img width="357" height="471" alt="Screenshot 2026-10-22 114602" src="https://github.com/user-attachments/assets/16aa28c2-f9f5-4fee-865e-09346bc11f8a" />




Truly a ransomware - 


<img width="702" height="402" alt="Screenshot 2026-10-22 114722" src="https://github.com/user-attachments/assets/a125cac7-b1f3-4b57-bb5e-e63ea7dbd882" />



And since the 1st question is asking for the specific path, any source output similar to this will lead us to the answer (and remember that iis logs are web server logs!)


<img width="454" height="74" alt="Screenshot 2026-10-22 115301" src="https://github.com/user-attachments/assets/84b184c7-8763-429f-8795-4c902ef4307b" />


I dug through that query above, and the image in this next screenshot stood out to me, and I'm pretty sure this is it! (I also answered the 2nd question easily before doing this question, and that eventID would've been just as good to figure this question out!)


<img width="593" height="146" alt="Screenshot 2026-10-22 115506" src="https://github.com/user-attachments/assets/ba8cf7d4-2e12-46b1-9088-e78df4f7ecc0" />






## What is the Sysmon event ID for the related file creation event?

``11``


## Can you find the MD5 hash of the ransomware?


Now I should look through Sysmon 1 to find the hash associated to the ransomware -
I also forgot to do, so I went back to the finding of the ransomware, and noted down the computer name of ```bellybear```


This query should easily lead me to the hash, either the location, or the exact filename.



<img width="696" height="79" alt="Screenshot 2026-10-22 120142" src="https://github.com/user-attachments/assets/23c198fa-a696-4377-95e6-6de9c9172ac1" />


Boom ```290c7dfb01e50cea9e19da81a781af2c```


<img width="556" height="106" alt="Screenshot 2026-10-22 120314" src="https://github.com/user-attachments/assets/8aca12cd-b28b-4631-87c7-1ccea4bbb382" />







## What file was saved to multiple folder locations?



I tried this query to discover patterns

<img width="470" height="102" alt="Screenshot 2026-10-22 120705" src="https://github.com/user-attachments/assets/b0be8789-968d-4d38-97bb-3b1f720c2f05" />



This is definitely it and the only pattern within this query ```readme.txt```. Attackers essentially placed Ransomware everywhere


<img width="835" height="576" alt="Screenshot 2026-10-22 120815" src="https://github.com/user-attachments/assets/b912e945-bd57-4030-87dc-904ec8ea8ec9" />





## What was the command the attacker used to add a new user to the compromised system?

**net user** is pretty common here, so let's search that

<img width="283" height="68" alt="Screenshot 2026-10-22 121051" src="https://github.com/user-attachments/assets/625d5d19-6920-49d1-ae93-289e43471c35" />


Boom ```net  user /add securityninja hardToHack123$```


<img width="345" height="28" alt="Screenshot 2026-10-22 121421" src="https://github.com/user-attachments/assets/efb7f61f-e6ac-4cb6-8be3-04c1b40f110e" />




## The attacker migrated the process for better persistence. What is the migration target (full image path)?

"the process" = added user (for context)

This is the command right after and I'm pretty certain this is the migration

<img width="472" height="35" alt="Screenshot 2026-10-22 121059" src="https://github.com/user-attachments/assets/6c1b1ab5-7f72-4b5e-8da2-d67ca7391c87" />


I never clicked on the TryHackMe hint, but it showed me anyways for this question that I should search Sysmon 8 (which I've never done), and it seems to prove useful for migration of processes!

``C:\Windows\System32\wbem\unsecapp.exe``

<img width="549" height="168" alt="Screenshot 2026-10-22 121947" src="https://github.com/user-attachments/assets/553ba5c2-2281-4798-858b-5402cf653c8a" />










## The attacker also injected into a system process to retrieve the hashes. What is the target process image used for getting the system hashes?

Let's go back to Sysmon 1

<img width="523" height="120" alt="Screenshot 2026-10-22 122302" src="https://github.com/user-attachments/assets/f6178334-9ea3-4924-a167-9a598d00c627" />

Interesting finding that I might need later


<img width="1017" height="30" alt="Screenshot 2026-10-22 122307" src="https://github.com/user-attachments/assets/1923b1bd-7879-460d-888d-f0cafe268454" />


Some findings were interesting, but the answer was apparently still in Sysmon 8


I honestly don't understand this, but I found it


```C:\Windows\System32\lsass.exe```

<img width="400" height="28" alt="Screenshot 2026-10-22 122708" src="https://github.com/user-attachments/assets/c0a75b61-c594-4db1-a172-ce76befc0907" />






## What is the web shell the exploit deployed to the system?


Web means let's go to ``iis`` logs now! The one essential that I forgot that IS NEEDED IS looking for POST requests, because the web shell was DEPLOYED TO SYSTEM

<img width="463" height="75" alt="Screenshot 2026-10-22 123218" src="https://github.com/user-attachments/assets/810c16fd-2bba-445e-831b-3a42c89206a7" />

The very bottom value looked suspicious

<img width="561" height="479" alt="Screenshot 2026-10-22 123344" src="https://github.com/user-attachments/assets/9a03b77b-d7a5-457f-a161-93b637a11cef" />


Was right to keep this in mind!!

```i3gfPctK1c2x.aspx```


<img width="1017" height="30" alt="Screenshot 2026-10-22 122307" src="https://github.com/user-attachments/assets/62f15d1a-cd52-4a6e-816c-ef4e402d8f33" />






## What is the command line that executed this web shell?



Let's search for the web shell now that we have it, and make sure that it's within Sysmon 1. The answer should be with that EventCode


<img width="623" height="223" alt="Screenshot 2026-10-22 123813" src="https://github.com/user-attachments/assets/7ab151f8-c751-46a8-ac3b-8f777d73a96f" />








## What three CVEs did this exploit leverage? Provide the answer in ascending order.

*external research*



# ```CVE-2018-13374```


**Overview**

• Vulnerability Type: Improper Access Control (CWE-732)
• CVSS Score: 4.3 (Medium)
• CISA Status: Listed in the CISA Known Exploited Vulnerabilities Catalog (added September 8, 2022)
• Vendor Advisory: Fortinet PSIRT Advisory FG-IR-18-157

**Affected Software**

• Fortinet FortiOS: Up to version 6.0.2 (excluding version 6.0.3), version 5.6.7 and earlier
• Fortinet FortiADC: Versions 5.4.0 through 5.4.4, 6.0.0 through 6.0.1, and 6.1.0

**Mechanism & Impact**

• What happens: An authenticated attacker with low privileges can capture configured LDAP server login credentials in plaintext.
• How it works: The vulnerability is triggered by redirecting an LDAP server connectivity test request (via modified parameters like /api/ldap) to a rogue/attacker-controlled LDAP server instead of the legitimate configured server.

**Remediation**

(Fortinet LDAP Info Leak)	Upgrade FortiOS to version 6.0.3 or later.
Upgrade FortiADC to patched branches.	Rotate LDAP credentials immediately if the device was exposed, as plaintext passwords may have been intercepted.



# ```CVE-2018-13379```

**Overview**

• Vulnerability Type: Path Traversal / Arbitrary File Read (CWE-22)
• CVSS Score: 9.8 (Critical) (NVD) / 9.1 (Fortinet)
• CISA Status: Listed in the CISA Known Exploited Vulnerabilities Catalog and the subject of multiple joint FBI-CISA cyber advisories.
• Vendor Advisory: Fortinet PSIRT Advisory FG-IR-18-384

**Affected Software**

The vulnerability exists when the SSL-VPN service (web-mode or tunnel-mode) is enabled.
• FortiOS: 6.0.0 to 6.0.4, 5.6.3 to 5.6.7, and 5.4.6 to 5.4.12
• FortiProxy: 1.0.0 to 1.0.7, 1.1.0 to 1.1.6, 1.2.0 to 1.2.8, and 2.0.0

**Mechanism & Impact**

• How it works: The FortiOS SSL-VPN web portal does not properly sanitize input parameters passed to its language customization endpoint (/remote/fgt_lang). An unauthenticated, remote attacker can send a specially crafted HTTP request using directory traversal sequences (../) to escape the web root and read internal system files.
• The Impact: Attackers primarily target the system file containing active web sessions (/dev/cmdb/sslvpn_websession). This file stores the usernames, cleartext passwords, and IP addresses of connected VPN users.
• Real-World Abuse: Because it requires no authentication to execute, threat actors routinely used it to scrape thousands of active credentials. In multiple high-profile incidents, lists containing tens of thousands of leaked corporate Fortinet VPN credentials stolen via this bug were dumped onto underground hacking forums

**Remediation**

Upgrade FortiOS to patched branches (e.g., 5.4.13, 5.6.14, 6.0.13, 6.2.9, or 6.4+).	1. Force a global password reset for all VPN users (patching does not invalidate stolen passwords).
2. Enforce MFA.
3. Check logs for ```/remote/fgt_lang?lang=``` to audit for past session theft.





# ```CVE-2020-0796```


**Overview**

• Vulnerability Type: Buffer Overflow via Integer Overflow (CWE-119 / CWE-190)
• CVSS Score: 10.0 (Critical)
• CISA Status: Listed in the CISA Known Exploited Vulnerabilities Catalog. CISA issued emergency alerts due to functional public proof-of-concept (PoC) code being actively used by attackers.
• Vendor Security Advisory: Disclosed out-of-band by Microsoft in March 2020 (ADV200005).

**Affected Software**

The vulnerability impacts the compression mechanism built into SMBv3.1.1:
• Windows 10: Versions 1903 and 1909 (32-bit, x64, and ARM64)
• Windows Server: Versions 1903 and 1909 (Server Core installations)

**Mechanism & Impact**

• How it works: The bug is located in the Srv2DecompressData function within the srv2.sys SMB kernel driver. When handling a specially crafted SMBv3 packet containing compressed data, a lack of strict bounds checking leads to an integer overflow during buffer allocation. This results in non-paged pool memory corruption.
• Dual Exposure (Server & Client):
	• Attacking a Server: An unauthenticated, remote attacker can send a malicious packet to a vulnerable server listening on TCP port 445.
	• Attacking a Client: A malicious server can exploit a vulnerable client computer that attempts to connect to it.
• The Impact: Successful execution grants the attacker SYSTEM-level privileges (the highest authorization level in Windows), allowing them to completely compromise the underlying OS, execute arbitrary commands, and move laterally across the network. Unsuccessful execution crashes the machine into a Blue Screen of Death (BSOD)


**Remediation**


Apply Microsoft cumulative update KB4551762.
Disable SMBv3 compression via Registry/PowerShell if patching is delayed. Block TCP Port 445 at the perimeter.







