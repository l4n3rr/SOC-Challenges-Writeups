## First attempt failed. Had to figure out why



<img width="759" height="476" alt="Fail" src="https://github.com/user-attachments/assets/71f65c8d-2420-4957-845e-6674003a3a80" />




## This gave me a hint to use the -m and call the domain!



<img width="677" height="256" alt="Another fail" src="https://github.com/user-attachments/assets/d1bd7d91-1025-4e26-b522-566dee833340" />




## SMB Server logs are not on Splunk, so I went here in Event Viewer, and apparently, one of the brute force attempts failed, and logged here as proof! I guess this also tells me that I have to allow a legacy module of SMB to work, so I can brute force with Hydra...




<img width="1538" height="508" alt="Success SMB Bruteforce maybe with proof of SMB legacy" src="https://github.com/user-attachments/assets/e77c732d-c9ef-40d2-8423-bb8e2699c4a8" />




## I decided to netexec enum as a random idea, and it showed on the logs! IP of my Attacker Box: 192.168.56.104




<img width="1472" height="432" alt="netexec enum found on endpoint logs" src="https://github.com/user-attachments/assets/41d8a851-5e66-4c31-ba1c-58c81212592b" />





## Unintentional side quest: Enumerate the DC with netexec, then find it on endpoint logs - COMPLETE

## I now have to find a way to brute force, either by an alternative method, or allow legacy SMB and go through with same method
