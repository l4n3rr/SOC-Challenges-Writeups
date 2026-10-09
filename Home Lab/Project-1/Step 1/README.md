<img width="759" height="476" alt="Fail" src="https://github.com/user-attachments/assets/71f65c8d-2420-4957-845e-6674003a3a80" />

First attempt failed. Had to figure out why



<img width="677" height="256" alt="Another fail" src="https://github.com/user-attachments/assets/d1bd7d91-1025-4e26-b522-566dee833340" />



This gave me a hint to use the -m and call the domain!




<img width="1538" height="508" alt="Success SMB Bruteforce maybe with proof of SMB legacy" src="https://github.com/user-attachments/assets/e77c732d-c9ef-40d2-8423-bb8e2699c4a8" />




This log location I did not make capture on Splunk, so I went here, and apparently, one of the brute force attempts failed, but logged here as proof! And with research, this won't work because it relies on SMB legacy module. All proof right here.




<img width="1472" height="432" alt="netexec enum found on endpoint logs" src="https://github.com/user-attachments/assets/41d8a851-5e66-4c31-ba1c-58c81212592b" />



I decided to netexec enum on this exact endpoint logs and when it succeeded, showed on the Event Viewer!
This turned into enumerating SMB on the DC and seeing it on the logs!
