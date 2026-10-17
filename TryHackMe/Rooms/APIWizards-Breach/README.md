## Context
Stange Web Requests, Nginx logs, unexpected system file changes, data on telegram, web scan on new API server

## Initial Access




**Which programming language is a web application written in?**

Clearly shows ```Python```

<img width="610" height="243" alt="Screenshot 2026-10-16 212115" src="https://github.com/user-attachments/assets/f7ec93b3-2bea-42ec-a031-32315db52ee6" />




**What is the IP address that attacked the web server?**

Let's move to nginx logs


<img width="1112" height="429" alt="Screenshot 2026-10-16 212212" src="https://github.com/user-attachments/assets/adeec61a-6ec4-4565-a7a3-35fa9486ede3" />

```149.34.244.142```


<img width="1138" height="215" alt="Screenshot 2026-10-16 212313" src="https://github.com/user-attachments/assets/f7e56316-b638-4799-bd69-15cb8072ff42" />







**Which vulnerability was found and exploited in the API service?**

During the search of this, I ended up in the API's cache and what I wrote was no longer legible, so I had to reboot 

<img width="1131" height="496" alt="Screenshot 2026-10-16 213132" src="https://github.com/user-attachments/assets/739829e0-8608-439d-841a-53d37e0c55d7" />
<img width="540" height="322" alt="Screenshot 2026-10-16 213319" src="https://github.com/user-attachments/assets/e03035f6-ebc6-40cb-8dca-7e88357c88de" />




The logs weren't telling me much until I realized that there are HTTP response codes. Silly me. Majority were failures of  directory enumeration, but these GET requests actually were successful. They were able to do recon commands after ```tz=test``` so I researched this, and this is a ```OS Command Injection``` vulnerability that was exploited.




<img width="655" height="240" alt="Screenshot 2026-10-16 214343" src="https://github.com/user-attachments/assets/00f999b7-456d-4e79-a350-3f3b9c5a23b2" />





**Which file contained the credentials used to privesc to root?**


I URL decoded and thought it was this

<img width="1132" height="120" alt="Screenshot 2026-10-16 214756" src="https://github.com/user-attachments/assets/c47b9e06-6654-4b73-a825-eb6ec85987f0" />
<img width="926" height="137" alt="Screenshot 2026-10-16 214801" src="https://github.com/user-attachments/assets/18c8246f-7dd7-420b-aecb-4ea0662ce586" />


Was not in the logs, it was in the apiservice directory in the beginning of this. Can't believe this flew over my head because I saw the credentials there.

```/home/dev/apiservice/src/config.py```


<img width="565" height="149" alt="Screenshot 2026-10-16 215459" src="https://github.com/user-attachments/assets/c323a2d6-fe48-4b93-a836-438e4facb3b8" />





**What file did the hacker drop and execute to persist on the server?**


Once again, I thought it was what I decoded, but I should be following logic. That file above leads to root now, so I can check the bash history on root



<img width="702" height="288" alt="Screenshot 2026-10-16 215809" src="https://github.com/user-attachments/assets/6f167ea1-e69e-410c-885b-9e502719764e" />


The **curl** and **wget** stand out. It made sense that it was the /tmp/ with the rooter2.
```/tmp/rooter2```

<img width="845" height="153" alt="Screenshot 2026-10-16 215838" src="https://github.com/user-attachments/assets/2bb40c0f-e63d-49e9-8bf0-805096220a32" />





**Which service was used to host the “rooter2” malware?**


In the same screenshot above contains the association with the rooter2 malware.

```transfer.sh```


*Note for me:* this is actually quite tough for me - this is an OS knowledge gap of just not knowing where to go -significant difference from windows - significant learning




## Futher Actions

**Which two system files were infected to achieve cron persistence?**


Where on Linux can I see cronjobs? - skill-gap keeps revealing itself. /etc/crontab


<img width="1030" height="380" alt="Screenshot 2026-10-17 094058" src="https://github.com/user-attachments/assets/5589ca9b-5f40-4c9d-a74d-43f45c078139" />


No idea how echo is revealing this valuable information and also no idea how it's infecting /etc/environment, BUT I AM NOW LEARNING THAT this is a bind shell with persistence. Theses listen for connections as C2 channel. 

```/etc/crontab```
```/etc/environment```



<img width="1017" height="118" alt="Screenshot 2026-10-17 094310" src="https://github.com/user-attachments/assets/f55fc9d8-74ca-463b-8ccb-4821f81053a5" />





**What is the C2 server IP address of the malicious actor?**

```5.230.66.147```

**What port is the backdoored bind bash shell listening at?**


I thought it was 1337 next to that IP address, but now I had to think beyond my knowledge of OS. How can I locate listening ports? ```ss``` (socket statistics) is the same as ```net``` (network) on Windows. ```ss -tulnp```. I'd like to break this down.

1. -t shows TCP sockets
2. -u shows UDP sockets
3. -l shows listening sockets only
4. -n shows numeric addresses
5. -p shows process using socket

Digging through the information shows ```nc```, which, if you're looking through *listening connections*, it's most likely Netcat.

Answer is ```3587```

<img width="1020" height="425" alt="Screenshot 2026-10-17 094941" src="https://github.com/user-attachments/assets/c054cbbb-111c-4971-93f3-c659791a73cf" />




**How does the bind shell persist across reboots?**


I had to look up (knowledge skill-gap), what allows shells to persist on reboots? The answer gave me ```systemdservice```



**What is the absolute path of the malicious service?**


Based on logic + previous information, I tried to connect it together.
```/etc/systemd/system/socket.service```



<img width="876" height="458" alt="Screenshot 2026-10-17 095751" src="https://github.com/user-attachments/assets/5a86bcce-3565-4316-9e07-70ca6daf4a51" />


I made sure by looking through ```socket.service``` and the answer was clear when I saw */bin/bash* + *nc*


<img width="507" height="229" alt="Screenshot 2026-10-17 095759" src="https://github.com/user-attachments/assets/f8bf2c22-eee4-48b9-8d21-81c431824845" />


## Even More Persistence


**Which port is blocked on the victim's firewall?**

I knew this required a certain command, but I didn't know what.

```iptables``` = Linux Firewall

```iptables -L``` = list all rules configured in iptables

```3578```


<img width="772" height="259" alt="Screenshot 2026-10-17 181011" src="https://github.com/user-attachments/assets/54fc0da4-607f-447b-9936-32968528d496" />




**How do the firewall rules persist across reboots?**


another command / location I didn't know how to get to.

```/root/.bashrc```
I actually saw this before, but I didn't understand how to read it.

<img width="627" height="125" alt="Screenshot 2026-10-17 181236" src="https://github.com/user-attachments/assets/9cd8a839-d271-4469-9828-88fd7becbdec" />




**How is the backdoored local Linux user named?**


```grep```ing /bin/bash in the ```/etc/passwd/``` location helped me find this

```support```

<img width="568" height="115" alt="Screenshot 2026-10-17 181545" src="https://github.com/user-attachments/assets/dbabe49e-16f8-4ebf-8e8f-57254b7da096" />





**Which privileged group was assigned to the user?**


needed to be a command that I didn't know about

```groups 'user'```
```support```

<img width="422" height="35" alt="Screenshot 2026-10-17 181811" src="https://github.com/user-attachments/assets/4ac2d181-9489-429f-b322-fb4ad86757bb" />




**What is the strange word on one of the backdoored SSH keys?**


I knew it was /.ssh/authorized_keys, however, my VM glitched so a restart had me stuck. It had to be on root to find those keys, which should be known by default. Silly me. Still, I struggled to find it, but narrowed it down to ```ntsvc```.

<img width="1062" height="363" alt="Screenshot 2026-10-17 182011" src="https://github.com/user-attachments/assets/af97d0a2-8294-4406-8963-9c5826d07ced" />


<img width="1006" height="42" alt="Screenshot 2026-10-17 182447" src="https://github.com/user-attachments/assets/8daf9fd8-7844-4853-8f57-2af20beef840" />







**Can you spot and name one more popular persistence method? Not a MITRE technique name.**


was never gonna get this one - ```SUID Binaries```

to find them, you'd do this command: ```find / -perm -u=s -type f 2>/dev/null```


**What are the original and the backdoored binaries from question 6?**


<img width="623" height="811" alt="Screenshot 2026-10-17 182658" src="https://github.com/user-attachments/assets/6f8db6a6-7bf2-42f1-ab17-4b48ddec60b9" />


This one required a keen eye and experience to spot the anomaly. I had no idea even when I cheated.

```/usr/bin/bash``` ```/usr/bin/clamav```






**What technique was used to hide the backdoor creation date?**


```Timestomping```, simple search. I didn't know how to search for creation date though.





## Final Target


**What file was dropped which contained gathered victim information?**

Start with ```ls -la```


**According to the dropped dump, what is the server’s kernel version?**


**Which active internal IPs were found by the “rooter2” network scan?**


**How did the hacker find an exposed HTTP index on another internal IP?**


**What command was used to exfiltrate the CDE database from the internal IP?**


**What is the most secret and precious string stored in the exfiltrated database?**





