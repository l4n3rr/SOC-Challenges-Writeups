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



And since the 1st question is asking for the specific path, any source output similar to this will lead us to the answer-





## What is the Sysmon event ID for the related file creation event?

``11``


## Can you find the MD5 hash of the ransomware?



## What file was saved to multiple folder locations?



## What was the command the attacker used to add a new user to the compromised system?



## The attacker migrated the process for better persistence. What is the migration target (full image path)?



## The attacker also injected into a system process to retrieve the hashes. What is the target process image used for getting the system hashes?



## What is the web shell the exploit deployed to the system?



## What is the command line that executed this web shell?



## What three CVEs did this exploit leverage? Provide the answer in ascending order.






