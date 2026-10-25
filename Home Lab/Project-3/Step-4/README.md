# Let's create test ticket in Jira

<img width="368" height="422" alt="Screenshot 2026-10-25 142349" src="https://github.com/user-attachments/assets/415aff12-d207-46ab-ae20-fe2f8085a0e3" />

## Let's now run the script and see if it succeeds. If it does, I should see a John Smith added to the Department of Finance OU, and slight changes to the Jira Ticket on my real machine Browser

<img width="682" height="166" alt="Screenshot 2026-10-25 142948" src="https://github.com/user-attachments/assets/2d10ab1d-f35f-4b24-b1f6-b13ae746882f" />


**first try, fail**

<img width="961" height="228" alt="Screenshot 2026-10-25 143011" src="https://github.com/user-attachments/assets/a24e8a5d-f98a-4e64-9dff-6b3a37714409" />

This might be the syntax error. Lets try again

<img width="106" height="277" alt="Screenshot 2026-10-25 143644" src="https://github.com/user-attachments/assets/44432934-3973-45f4-94a7-02375564d070" />
<img width="310" height="57" alt="Screenshot 2026-10-25 143750" src="https://github.com/user-attachments/assets/6bb17ffe-fd53-4087-8a7b-cb06f08ae701" />




**Another error, and my guess is, the only way to resolve my domain is to have active connection on the VM (which I don't, my VM is on host-only adapter network), and ensure that DNS works (since the script deponds on my Jira domain). Let's make these changes**

<img width="308" height="44" alt="Screenshot 2026-10-25 143849" src="https://github.com/user-attachments/assets/aef9d470-0ef0-4cda-934c-ed28fcbad08b" />


I swapped to have active connection, but I still had to troubleshoot the root problem. Going into the DNS server configuration within ```dnsmgmt.msc```, DNS basically had no forwarders. For this lab, I needed to setup a forwarder, and it's as simple as creating a forwarder that points to google's IP ```8.8.8.8``` so that it could use google to look for my domain!



## Let's power the VM with connection this time, ping google.com and my own domain, then try again

I confirmed that I have active connection, now I need to ensure with a new API token, that it is reach-able and able to connect to Jira.

<img width="560" height="58" alt="684175053-0e830eee-f001-437d-bba3-455ab26a0d7a" src="https://github.com/user-attachments/assets/f088fc99-6395-4abd-a51b-2399754b3c2c" />


Let's try one more time! Please work. Nope.
<img width="891" height="39" alt="Screenshot 2026-10-25 150233" src="https://github.com/user-attachments/assets/0b4da468-4185-43a3-9cb6-1bf785be8dcc" />


Script works I believe!!! Still have to troubleshoot, but it's progress

<img width="628" height="64" alt="Screenshot 2026-10-25 150818" src="https://github.com/user-attachments/assets/898fa5b0-460d-41a7-a998-c11f640bd69d" />


The script looks for the project as well as a keyword within my ticket summary such as "Onboard" which is so that it knows to go through with the script and can find the ticket

When I made 2 service request onboarding tickets, these are the keys, and the script is looking for the IO, but I have it set to IT. Also the ticket should have "onboard" within the summary!

<img width="60" height="157" alt="Screenshot 2026-10-25 151346" src="https://github.com/user-attachments/assets/084f1c1a-fbf5-4960-bcf1-a7dacbbbcbc4" />

Here is the exact line: ```$JqlQuery = 'project = "IO" AND status != "Done" AND summary ~ "Onboard"'```

Original below:


<img width="616" height="41" alt="Screenshot 2026-10-25 151644" src="https://github.com/user-attachments/assets/6cd85e2c-765e-40cb-9757-0d32e364fb95" />



New below:

<img width="589" height="43" alt="Screenshot 2026-10-25 151710" src="https://github.com/user-attachments/assets/751d89ea-51fe-439a-bc59-57a96dcb01ae" />


New ticket:

<img width="368" height="61" alt="Screenshot 2026-10-25 151755" src="https://github.com/user-attachments/assets/99f1bf69-92c8-4367-a495-47271f19fad4" />


I had to do more troubleshooting, but BOOM!

<img width="740" height="101" alt="Screenshot 2026-10-25 152147" src="https://github.com/user-attachments/assets/7e03ab8d-3681-4060-b364-3078e040be2a" />


**Let's now do full confirmation within the Jira ticket + AD within DC**

```not perfect, but the success of the automation bridge is the most important```

*i didnt show it, but it even contained the temporary password that the script said it would generate*

<img width="249" height="169" alt="Screenshot 2026-10-25 152414" src="https://github.com/user-attachments/assets/845d2f11-e276-45b9-95c6-99e0708fe126" />

<img width="350" height="46" alt="Screenshot 2026-10-25 152427" src="https://github.com/user-attachments/assets/c3114f28-240e-4e3c-9f61-af8b4cdf02e6" />


<img width="141" height="36" alt="Screenshot 2026-10-25 152430" src="https://github.com/user-attachments/assets/2f58e761-a199-42af-a034-50c0cd5e4377" />


<img width="143" height="37" alt="Screenshot 2026-10-25 152318" src="https://github.com/user-attachments/assets/f1c28e07-1725-423b-af3a-223bda979641" />



## In a real corporate environment, pressed on time, I'd just easily configure and re-write the names to ensure that they're correct. Without being pressed on time, I'd take that time to fix the script and it's naming / logic so I wouldn't have to manually do it every time
