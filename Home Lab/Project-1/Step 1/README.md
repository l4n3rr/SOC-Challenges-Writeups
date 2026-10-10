## First attempt failed. Had to figure out why



<img width="759" height="476" alt="Fail" src="https://github.com/user-attachments/assets/71f65c8d-2420-4957-845e-6674003a3a80" />




## This gave me a hint to use the -m and call the domain!



<img width="677" height="256" alt="Another fail" src="https://github.com/user-attachments/assets/d1bd7d91-1025-4e26-b522-566dee833340" />




## SMB Server logs are not on Splunk, so I went here in Event Viewer, and apparently, one of the brute force attempts failed, and logged here as proof! I guess this also tells me that I have to allow a legacy module of SMB to work, so I can brute force with Hydra...




<img width="1538" height="508" alt="Success SMB Bruteforce maybe with proof of SMB legacy" src="https://github.com/user-attachments/assets/e77c732d-c9ef-40d2-8423-bb8e2699c4a8" />




## I decided to netexec enum as a random idea, and it showed on the logs! IP of my Attacker Box: 192.168.56.104




<img width="1472" height="432" alt="netexec enum found on endpoint logs" src="https://github.com/user-attachments/assets/41d8a851-5e66-4c31-ba1c-58c81212592b" />





## Unintentional side quest: Enumerate the DC with netexec, then find it on endpoint logs - COMPLETE

## I now have to find a way to brute force, and decided to do it with Metasploit because it handles modern SMB protocols!





<img width="670" height="154" alt="Screenshot (36)" src="https://github.com/user-attachments/assets/e3e52dea-5ddb-463d-963f-fc4d4efe2936" />
<img width="684" height="104" alt="Screenshot (37)" src="https://github.com/user-attachments/assets/0f068696-6a0d-4143-901d-21f21f304e24" />



## I just had to set the RHOSTS (target), SMB User, Password List, StopOnceSucceeded. 




<img width="887" height="921" alt="Screenshot (38)" src="https://github.com/user-attachments/assets/cfd360e4-b2b4-4cee-ae6c-0c1f41fecff5" />
<img width="557" height="404" alt="Screenshot (39)" src="https://github.com/user-attachments/assets/301a326e-79c8-412c-af09-ac792a256561" />
<img width="387" height="240" alt="Screenshot (40)" src="https://github.com/user-attachments/assets/64ac55d6-4997-45cd-b358-6628b5396301" />




