So from Nmap, the only port open is 445 - SMB


<img width="767" height="615" alt="Screenshot (109)" src="https://github.com/user-attachments/assets/e76e8238-942c-4077-8cf5-47eab586b3b4" />



I ran a script ```smb2-security-mode``` on my Win-10 to reveal that SMB message signing is enabled **but not required**. I saw this and said *maybe I can exploit this*



<img width="711" height="368" alt="Screenshot (110)" src="https://github.com/user-attachments/assets/6385c046-1ec5-46aa-861e-e20af1ba73aa" />



I went down this rabbit hole path to see what I could do with that possible vulnerability. I used ``Responder`` to capture network authentication traffic, and this specifically listens for any failed name resolutions on Win-10 as my target!


<img width="777" height="855" alt="Screenshot (111)" src="https://github.com/user-attachments/assets/186e64fd-5380-4611-b9d7-2a32483290b9" />



I proceed to mistype name resolutions to see output.



<img width="1534" height="727" alt="Screenshot (112)" src="https://github.com/user-attachments/assets/d5630b7b-068f-4098-944e-1058637ad5ff" />




I didn't see any valuable information until I attempted the mistyped name resolution with credentials this time. Look what I got! I didn't understand the responder fully, so I got a step by step breakdown from beginning, to the hash capture.



1. I'm cheating, already being on the network, so on the network I'm listening for any failed name resolutions (DNS)
2. DNS fails, but the workstation typically wont give up, and try legacy broadcast protocols
3. Workstation asks network if anyone knows where the mistyped path is
4. Responder acts as the imposter - 'I do! Send to me'
5. Victim tries again with credentials, and hands me the hash



<img width="1567" height="478" alt="Screenshot (113)" src="https://github.com/user-attachments/assets/5bba26af-1e0b-438c-8414-44ff332a594e" />




I proceed to disable HTTP/SMB server within ```Responder.conf``` so it doesn't conflict with the relay I will attempt with ```ntlmrelayx.py```.

I'll now use ```MultiRelay.py``` (Impacket) to feed authentication live to another machine. **THIS ONLY WORKS IF SMB SIGNING IS NOT REQUIRED**.



<img width="471" height="488" alt="Screenshot (114)" src="https://github.com/user-attachments/assets/eeb63912-d6b0-4122-aaf2-ede4c326c77a" />




Locate ```ntlmrelayx.py``` (script for relaying)



<img width="799" height="366" alt="Screenshot (115)" src="https://github.com/user-attachments/assets/914241a9-ae2b-4380-bf99-726a25aabb25" />




Start ntlmrelayx.py so it starts waiting for connections

```smb://192.168.56.103``` -c "net user backdoor LabPassword123! /add /domain"

I believe if this works, it instantly creates a backdoor domain user




<img width="797" height="482" alt="Screenshot (116)" src="https://github.com/user-attachments/assets/3ec9fff7-9d03-4921-9718-c0fd0ab1add5" />



Start Responder and ensure that SMB/HTTP server is off




<img width="779" height="708" alt="Screenshot (117)" src="https://github.com/user-attachments/assets/29d9ca40-9e56-4335-8c41-3905ae28cecb" />




Mistype path in Win-10 and see the output of both terminals



<img width="1615" height="261" alt="Screenshot (118)" src="https://github.com/user-attachments/assets/1e34e4df-17ee-4223-8468-78aa71e22ce8" />




Failed... it worked better when I attempted again with -smb2support, still failed...



<img width="788" height="216" alt="Screenshot (119)" src="https://github.com/user-attachments/assets/d130790b-0f99-436f-a046-9a4de66b1f76" />




## This is a failure because the Windows10 Workstation is my bait machine, not my relay receiver (proof by my smb://192.168.56.103 syntax)

1. Win10 is the bait of the authentication
2. Kali intercepts handshake with Responder
3. Kali relays that to another machine where SMB signing is disabled (relayed by the ```ntlmrelayx.py```)
4. Assumes legit user login
5. Runs commands immediately

## What I did in a few words is --- I captured the handshake of Win10 Machine, then relayed that information back to Win10 *when it should've been toward another machine* FAILED

## Let's move on to cracking the hash


I learned late that I needed to copy and paste *the entire hash*. I remember an answer on THM only working with part of an entire blob instead of the entire blob and applied it here. I learned otherwise in my homelab.


<img width="634" height="422" alt="Screenshot 2026-10-20 102952" src="https://github.com/user-attachments/assets/e42db3a8-7822-4fe8-9853-bf5d0b82b32e" />



Hashcat didn't work and needed to install stuff that I wasn't able to install (attempt, failed). Let's try John the Ripper



<img width="711" height="408" alt="Screenshot 2026-10-20 103613" src="https://github.com/user-attachments/assets/717d9d0a-601d-40c6-8094-ba57bb9c2c59" />


It apparently recognized my password hash, but I guess it just didn't crack. The thing is, the rockyou.txt list may not have my actual jdoe password, so let's create my own wordlist and ensure that it's in there




<img width="732" height="141" alt="Screenshot 2026-10-20 104837" src="https://github.com/user-attachments/assets/921438bb-f31c-4612-9ec8-1f7f6375c4aa" />





<img width="143" height="52" alt="Screenshot 2026-10-20 105734" src="https://github.com/user-attachments/assets/46992369-2a4c-47a3-bbfc-88669acefaf0" />


The crack kept failing. The good thing is my 'possible solutions' was still narrowing down the problem. My own wordlist with the right password in there AND making sure there are no unnecessary spaces or lines. *Still didn't understand why* **UNTIL** - ```all I had to fucking do, is make sure that the hash file does not have any unnecessary spaces or lines in it```


<img width="777" height="250" alt="Screenshot 2026-10-20 110223" src="https://github.com/user-attachments/assets/64df9d7c-e380-45d7-80f3-b33f9db55177" />




Bingo



<img width="798" height="320" alt="Screenshot 2026-10-20 111948" src="https://github.com/user-attachments/assets/beb79e7e-6f9d-455a-8210-b2b4e180908d" />




## I also completely forgot that while I have my splunk forwarders forwarding, it doesn't matter for this process, because it was all done offline.


## Let's enumerate with BloodHound



Failures all around.



<img width="1548" height="502" alt="Screenshot 2026-10-20 183105" src="https://github.com/user-attachments/assets/1260c1c7-2955-4967-a734-e69be63565eb" />

<img width="347" height="68" alt="Screenshot 2026-10-20 183338" src="https://github.com/user-attachments/assets/246b8b95-b606-4a92-967c-3a7ce72cf149" />




I increased my chances of success by pointing the nameserver of Kali Linux to the DC IP

<img width="306" height="91" alt="Screenshot 2026-10-20 184040" src="https://github.com/user-attachments/assets/057c0abb-b888-4fd9-ad81-2a20053c94d5" />


Still failures all around with adjustments in my commands


<img width="1414" height="134" alt="Screenshot 2026-10-20 184206" src="https://github.com/user-attachments/assets/b4efc161-cb05-4b6c-a4c1-2c12f2d8e4aa" />
<img width="871" height="48" alt="Screenshot 2026-10-20 184504" src="https://github.com/user-attachments/assets/f5363744-349a-4374-94c3-35557af1b0ed" />

<img width="881" height="86" alt="Screenshot 2026-10-20 184824" src="https://github.com/user-attachments/assets/93772d70-a4b1-44a7-915b-bfcb524917ba" />

<img width="796" height="42" alt="Screenshot 2026-10-20 190221" src="https://github.com/user-attachments/assets/758ff494-2310-4961-b050-79dbb0db3f68" />


All to no avail. After these failed attempts, I decided to stop attacking because the true thing I wanted to do, was monitor and defend on splunk with EVERYTHING I've done. 












