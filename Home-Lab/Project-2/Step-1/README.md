# Recon with Nmap

## Part 1
I wrote down what I thought was the most important syntax for fundamental Nmap usage.



-sL -> list targets to scan


-sn -> Ping Scan


-sS -> TCP Syn Scan


-sU -> UDP Scan


-sV -> Service/Version Info


-O -> OS Detection


-S -> Spoof Source Address (probably not needed but wanted to experiment)


-v -> Verbosity


--reason -> Port State


-T(0-5) -> Speed of Recon Process


-p -> Specify Port


## Part 2
Ensure that my target range is correct with ```-sL```



<img width="888" height="700" alt="Screenshot (78)" src="https://github.com/user-attachments/assets/e876bd9f-517a-4849-b3ff-e2e8db42733d" />




Let's ping all hosts to see which are online with ```-sn```



<img width="933" height="318" alt="Screenshot (79)" src="https://github.com/user-attachments/assets/a706e7a0-91b6-49c0-a464-a3c8a96bdce5" />





Let's now do a TCP SYN scan on our Windows10 Workstation with ```-sS```


You'll see that I tried to specify 

ports

speed of recon

spoof my address 


**I got this error message. I have no idea how to spoof addresses I guess. Also, when I specified ports, I added an unnecessary space which resulted in invalid syntax**


<img width="960" height="179" alt="Screenshot (80)" src="https://github.com/user-attachments/assets/b43f291f-030a-4d28-9a5f-49e246bee6a2" />




Let's try the same TCP SYN scan with OS Detection, verbosity, and no specification of ports!


A lot of information. I was wondering why, and it's a 50% mix of ```-O``` and ```-v``` I believe



<img width="928" height="598" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/956121b0-06ff-4bcd-aba2-1c2c612fa662" />




This time, I decided to do a TCP SYN scan AND a UDP scan, specifying ports for Kerberos, LDAP(S), WinRM, SMB and give reason for the port state.
``-sU -sS --reason -p``





<img width="568" height="527" alt="Screenshot (87)" src="https://github.com/user-attachments/assets/8d57980a-a4df-489b-806a-073f232b0a9e" />


Learned that ``filtered`` means the windows firewall blocked these connections -  whereas ``open | filtered`` means Nmap is not sure, so it leaves you guessing.

I don't have screenshot, but I used this command: ```Set-NetFireWallProfile -Profile Domain, Public, Private -Enabled false``` to disable the firewall on my Windows10 Workstation to hopefully allow these Nmap connections for the sake of experimentation. 


