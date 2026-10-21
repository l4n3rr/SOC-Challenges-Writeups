Let's begin broadly and by the EventCode across the board, plenty here.


<img width="917" height="647" alt="Screenshot 2026-10-20 195242" src="https://github.com/user-attachments/assets/064839c8-ec6a-427d-971b-feeed17bc126" />

<img width="917" height="531" alt="Screenshot 2026-10-20 195804" src="https://github.com/user-attachments/assets/5362af41-437a-40d3-9917-4ae6cf6fc2e2" />


After a bit of scrolling, this can be the DNS failures I got from BloodHound, but since it wasn't successful, it's not a hard-log


<img width="624" height="565" alt="Screenshot 2026-10-20 200104" src="https://github.com/user-attachments/assets/86ce8003-aa0e-4f65-8038-2f8f8b22481d" />

Let's search for successful logons with jdoe, including the IP of the Kali Linux Attack Box


<img width="902" height="469" alt="Screenshot 2026-10-20 212738" src="https://github.com/user-attachments/assets/c197e63f-94ca-4197-a15d-7d1d6ef61b8f" />


Found the logs including the NTLMv2!!

5 events of these, and this generated during the ```Responder``` phase when I captured the NTLMv2 hash!


<img width="391" height="538" alt="Screenshot 2026-10-20 213051" src="https://github.com/user-attachments/assets/0b1f330e-2288-4dd7-b756-6f32db864365" />

