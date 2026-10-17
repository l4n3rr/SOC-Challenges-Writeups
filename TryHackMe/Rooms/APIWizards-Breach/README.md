## Context
Stange Web Requests, Nginx logs, unexpected system file changes, data on telegram, web scan on new API server

## Initial Access




Which programming language is a web application written in?

Clearly shows ```Python```

<img width="610" height="243" alt="Screenshot 2026-10-16 212115" src="https://github.com/user-attachments/assets/f7ec93b3-2bea-42ec-a031-32315db52ee6" />




What is the IP address that attacked the web server?

Let's move to nginx logs


<img width="1112" height="429" alt="Screenshot 2026-10-16 212212" src="https://github.com/user-attachments/assets/adeec61a-6ec4-4565-a7a3-35fa9486ede3" />

```149.34.244.142```


<img width="1138" height="215" alt="Screenshot 2026-10-16 212313" src="https://github.com/user-attachments/assets/f7e56316-b638-4799-bd69-15cb8072ff42" />







Which vulnerability was found and exploited in the API service?

During the search of this, I ended up in the API's cache and what I wrote was no longer legible, so I had to reboot 

<img width="1131" height="496" alt="Screenshot 2026-10-16 213132" src="https://github.com/user-attachments/assets/739829e0-8608-439d-841a-53d37e0c55d7" />
<img width="540" height="322" alt="Screenshot 2026-10-16 213319" src="https://github.com/user-attachments/assets/e03035f6-ebc6-40cb-8dca-7e88357c88de" />




The logs weren't telling me much until I realized that there are HTTP response codes. Silly me. Majority were failures of  directory enumeration, but these GET requests actually were successful. They were able to do recon commands after ```tz=test``` so I researched this, and this is a ```OS Command Injection``` vulnerability that was exploited.




<img width="655" height="240" alt="Screenshot 2026-10-16 214343" src="https://github.com/user-attachments/assets/00f999b7-456d-4e79-a350-3f3b9c5a23b2" />





Which file contained the credentials used to privesc to root?


I URL decoded and thought it was this

<img width="1132" height="120" alt="Screenshot 2026-10-16 214756" src="https://github.com/user-attachments/assets/c47b9e06-6654-4b73-a825-eb6ec85987f0" />
<img width="926" height="137" alt="Screenshot 2026-10-16 214801" src="https://github.com/user-attachments/assets/18c8246f-7dd7-420b-aecb-4ea0662ce586" />


Was not in the logs, it was in the apiservice directory in the beginning of this. Can't believe this flew over my head because I saw the credentials there.

```/home/dev/apiservice/src/config.py```


<img width="565" height="149" alt="Screenshot 2026-10-16 215459" src="https://github.com/user-attachments/assets/c323a2d6-fe48-4b93-a836-438e4facb3b8" />





What file did the hacker drop and execute to persist on the server?


Once again, I thought it was what I decoded, but I should be following logic. That file above leads to root now, so I can check the bash history on root



<img width="702" height="288" alt="Screenshot 2026-10-16 215809" src="https://github.com/user-attachments/assets/6f167ea1-e69e-410c-885b-9e502719764e" />


The **curl** and **wget** stand out. It made sense that it was the /tmp/ with the rooter2.
```/tmp/rooter2```

<img width="845" height="153" alt="Screenshot 2026-10-16 215838" src="https://github.com/user-attachments/assets/2bb40c0f-e63d-49e9-8bf0-805096220a32" />





Which service was used to host the “rooter2” malware?


In the same screenshot above contains the association with the rooter2 malware.

```transfer.sh```


*Note for me:* this is actually quite tough for me - this is an OS knowledge gap of just not knowing where to go -significant difference from windows - significant learning




## Futher Actions




