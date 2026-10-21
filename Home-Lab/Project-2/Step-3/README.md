Let's begin broadly and by the EventCode across the board, plenty here.


<img width="917" height="647" alt="Screenshot 2026-10-20 195242" src="https://github.com/user-attachments/assets/064839c8-ec6a-427d-971b-feeed17bc126" />

<img width="917" height="531" alt="Screenshot 2026-10-20 195804" src="https://github.com/user-attachments/assets/5362af41-437a-40d3-9917-4ae6cf6fc2e2" />


After a bit of scrolling, this can be the DNS failures I got from BloodHound, but since it wasn't successful, it's not a hard-log


<img width="624" height="565" alt="Screenshot 2026-10-20 200104" src="https://github.com/user-attachments/assets/86ce8003-aa0e-4f65-8038-2f8f8b22481d" />

Let's search for successful logons with jdoe, including the IP of the Kali Linux Attack Box


<img width="902" height="469" alt="Screenshot 2026-10-20 212738" src="https://github.com/user-attachments/assets/c197e63f-94ca-4197-a15d-7d1d6ef61b8f" />


Found the logs including the NTLMv2!!

5 events of these, and this generated during the ```Responder``` phase when I captured the NTLMv2 hash!

### NEW DETECTION RULE FOR NTLM NETWORK LOGONS HITTING DC
```4624, LogonType=3, AuthenticationPackage=NTLM```


<img width="391" height="538" alt="Screenshot 2026-10-20 213051" src="https://github.com/user-attachments/assets/0b1f330e-2288-4dd7-b756-6f32db864365" />



## Takeaways

Probably the biggest things I learned is 
1. Legacy protocols may still be active, and should be disabled (Responder worked off of legacy protocols, and in this case, can be achieved via Group Policy ```turn off multicast name resolution```
2. I didn't even know that if DNS didn't work, the OS doesn't give up on the attempts of name resolution and uses legacy protocol bs
3. Enforcing Signing (SMB/LDAP) is essential to preventing attackers siting in the middle of a conversation (Responder as an example) and attaining information that can further their attack. TLDR; Signing keeps data authentic and untampered
4. Logon Type is really good at determining the scope and what you're up against
5. Attacking takes insane persistence to push through failures thousands and thousands of times



