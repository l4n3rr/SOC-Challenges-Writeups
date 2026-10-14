<img width="928" height="598" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/665c9203-9627-41a2-ac3e-81bd1d4ccaf0" /># Recon with Nmap

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


A lot of information. I was wondering why, and it's a 50% mix of ```-O``` and ```-v```



<img width="928" height="598" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/956121b0-06ff-4bcd-aba2-1c2c612fa662" />






