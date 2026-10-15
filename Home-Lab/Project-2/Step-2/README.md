So from Nmap, the only port open is 445 - SMB


<img width="767" height="615" alt="Screenshot (109)" src="https://github.com/user-attachments/assets/e76e8238-942c-4077-8cf5-47eab586b3b4" />



I ran a script ```smb2-security-mode``` on my Win-10 to reveal that SMB message signing is enabled **but not required**. I saw this and said *maybe I can exploit this*



<img width="711" height="368" alt="Screenshot (110)" src="https://github.com/user-attachments/assets/6385c046-1ec5-46aa-861e-e20af1ba73aa" />



I went down this rabbit hole path to see what I could do with that possible vulnerability. I used ``Responder`` to capture network authentication traffic, and this specifically listens for any failed name resolutions on Win-10 as my target!


Screenshot 111


I proceed to mistype name resolutions to see output.



Screenshot 112



I didn't see any valuable information until I attempted the mistyped name resolution with credentials this time. Look what I got! I didn't understand the responder fully, so I got a step by step breakdown from beginning, to the hash capture.



1. I'm cheating, already being on the network, so on the network I'm listening for any failed name resolutions (DNS)
2. DNS fails, but the workstation typically wont give up, and try legacy broadcast protocols
3. Workstation asks network if anyone knows where the mistyped path is
4. Responder acts as the imposter - 'I do! Send to me'
5. Victim tries again with credentials, and hands me the hash



Screenshot 113



I proceed to disable HTTP/SMB server within ```Responder.conf``` so it doesn't conflict with the relay I will attempt with ```ntlmrelayx.py```.

I'll now use ```MultiRelay.py``` (Impacket) to feed authentication live to another machine. **THIS ONLY WORKS IF SMB SIGNING IS NOT REQUIRED**.



Screenshot 114



Locate ```ntlmrelayx.py``` (script for relaying)



Screenshot 115




Start ntlmrelayx.py so it starts waiting for connections

```smb://192.168.56.103``` -c "net user backdoor LabPassword123! /add /domain"

I believe if this works, it instantly creates a backdoor domain user




Screenshot 116



Start Responder and ensure that SMB/HTTP server is off




Screenshot 117




Mistype path in Win-10 and see the output of both terminals



Screenshot 118



Failed... it worked better when I attempted again with -smb2support, still failed...



Screenshot 119




## This is a failure because the Windows10 Workstation is my bait machine, not my relay receiver (proof by my smb://192.168.56.103 syntax)

1. Win10 is the bait of the authentication
2. Kali intercepts handshake with Responder
3. Kali relays that to another machine where SMB signing is disabled (relayed by the ```ntlmrelayx.py```)
4. Assumes legit user login
5. Runs commands immediately

## What I did in a few words is --- I captured the handshake of Win10 Machine, then relayed that information back to Win10 *when it should've been toward another machine*










