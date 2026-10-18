# SOC LVL2 Capstone Challenge Room


## Context

* Artefacts used from a real world cyber attack, careful
* PandaProbe Intelligence
* Notification appears on CTI platform
* Raised concerns about malware threats
* A ticket was submitted, which contained file attachments, presumed to be malware samples

Expectation Process:
1. Downloading the malware samples provided in the ticket, ensuring they were contained in a secure environment.
2. Running the samples through preliminary automated malware analysis tools to get a quick overview.
3. Deep diving into a manual analysis, understanding the malware's behaviour, and identifying its communication patterns.
4. Correlating findings with global threat intelligence databases to identify known signatures or behaviours.
5. Compiling a comprehensive report with mitigation and recovery steps, ensuring SwiftSpend Finance could swiftly address potential threats.


*Damn this connection is slow*



This is my message to investigate with the password ```Panda321!``` for archive, and this malware being a suspected RAT

<img width="543" height="737" alt="Screenshot 2026-10-18 182258" src="https://github.com/user-attachments/assets/ab6068d5-18e0-4774-9e3b-94b89a6d35a3" />
<img width="547" height="668" alt="Screenshot 2026-10-18 182305" src="https://github.com/user-attachments/assets/828bffc2-c945-40c8-a2cd-128ee3dfde66" />


Let's understand what we're working with first



<img width="252" height="539" alt="Screenshot 2026-10-18 182640" src="https://github.com/user-attachments/assets/da6cf296-6135-4603-b92d-11d91cb29294" />



Clicking the document of that message led to these DLLs




<img width="847" height="540" alt="Screenshot 2026-10-18 183007" src="https://github.com/user-attachments/assets/33994026-0004-4ef6-a3d5-e703d0604a68" />


Unzipped!




<img width="785" height="437" alt="Screenshot 2026-10-18 183145" src="https://github.com/user-attachments/assets/566d2525-a510-4286-a1b6-39fe137045a3" />






**Who shared the malware samples?**


Simply see who sent the email - ```Oliver Bennett```



<img width="409" height="225" alt="Screenshot 2026-10-18 183501" src="https://github.com/user-attachments/assets/f6c76841-7f7e-45ae-b4e6-9858e2b9fe95" />






**What is the SHA1 hash of the file "pRsm.dll" inside samples.zip?**



<img width="573" height="39" alt="Screenshot 2026-10-18 183220" src="https://github.com/user-attachments/assets/9f2653b1-6170-42dd-9bcd-246ecc665a53" />





**Which malware framework utilizes these DLLs as add-on modules?**


This required external research, which these DLLs are used by the ```Evasive Panda``` APT.
The malware framework is ```MgBot``` and this is what each DLL does.


• wcdbcrk.dll: Tencent WeChat information stealer


• qmsdp.dll: Tencent QQ chat history & database stealer


• cbmrpa.dll: Clipboard text and USB data logger


• pRsm.dll: Input/output audio stream capturer


• maillfpassword.dll: Outlook and Foxmail credential stealer









**Which MITRE ATT&CK Technique is linked to using pRsm.dll in this malware framework?**


More external research. Let's search ```Input/output audio stream capturer, which MITRE ATT&CK Technique is related?```

```T1123```





**What is the CyberChef defanged URL of the malicious download location first seen on 2020-11-02?**







**What is the CyberChef defanged IP address of the C&C server first detected on 2020-09-14 using these modules?**






**What is the md5 hash of the spyagent family spyware hosted on the same IP targeting Android devices in Jun 2025?**

