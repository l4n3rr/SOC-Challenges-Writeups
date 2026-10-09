This step only involved 3 VMs (although, I didn't need the Kali Linux VM. I could've done it all with Splunk GUI on the DC)

For now, the only Splunk Forwarder running is on the Windows 11 DC

1. Ubuntu Victim Box (hosting Splunk server)
2. Windows 11 DC
3. Kali Linux Attacker Box

Is Splunk running? Yes, with -> sudo /opt/splunk/bin/splunk -start --run-as-root

<img width="1286" height="543" alt="Screenshot (22)" src="https://github.com/user-attachments/assets/cca63556-07da-4d48-a1c3-722fac5c31c7" />


Is Splunk Forwarder running? Yes, with Powershell -> Test-NetConnection -ComputerName [IP of Machine Hosting Splunk Server] -Port 9997

<img width="985" height="133" alt="Screenshot (23)" src="https://github.com/user-attachments/assets/9cdf70db-32fb-4fc1-89e7-027e71809a6a" />

