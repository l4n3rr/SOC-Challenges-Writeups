## This step only involved 3 VMs (although, I didn't need the Kali Linux VM. I could've done it all with Splunk GUI on the DC)

# For now, the only Splunk Forwarder running is on the Windows 11 DC

1. Ubuntu Victim Box (hosting Splunk server)
2. Windows 11 DC
3. Kali Linux Attacker Box

#Is Splunk running? Yes, with -> sudo /opt/splunk/bin/splunk -start --run-as-root



<img width="1286" height="543" alt="Screenshot (22)" src="https://github.com/user-attachments/assets/cca63556-07da-4d48-a1c3-722fac5c31c7" />



#Is Splunk Forwarder running? Yes, with Powershell -> Get-Service SplunkForwarder


<img width="985" height="133" alt="Screenshot (23)" src="https://github.com/user-attachments/assets/9cdf70db-32fb-4fc1-89e7-027e71809a6a" />


#Really cool seeing the logs and discovering my computer name for both Windows Machines


<img width="1823" height="613" alt="Screenshot (25)" src="https://github.com/user-attachments/assets/383a693b-5a0b-4638-9693-987f4e0d825e" />
<img width="1709" height="509" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/817792f7-203c-4fd4-a461-2c01aef069d2" />




#Despite this, it took me 20 minutes to see if logs were coming in live. I tested it with the 'whoami' command.


<img width="1541" height="294" alt="Screenshot (28)" src="https://github.com/user-attachments/assets/63382f84-daf8-4c32-bdd3-0ab02b6c4728" />



#Really cool to see side to side! However, it didn't feel like this was coming in live. After some digging...


<img width="1920" height="174" alt="Screenshot (29)" src="https://github.com/user-attachments/assets/4a452082-6be1-4519-a1cd-76d65502ccf0" />
<img width="1920" height="173" alt="Screenshot (30)" src="https://github.com/user-attachments/assets/6d445132-a185-45d9-8a13-1166073776b5" />
<img width="1920" height="216" alt="Screenshot (31)" src="https://github.com/user-attachments/assets/0236d211-a36d-4164-b213-654dd776de2a" />



#All I had to do was set the TIME RANGE in SPLUNK within "Real-Time" rather than "Relative"! It was weird that it wasn't showing exact logs in the one minute window, but it started showing all live logs and details of them with the "All-Time" option.

##That was an OH SHIT moment




