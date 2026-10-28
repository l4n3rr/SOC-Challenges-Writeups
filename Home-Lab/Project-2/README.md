# AD Full Chain-Attack and Defend (semi-fail)

0.5. [Mini AD Project](./Step-0.5/README.md)

1. [Recon](./Step-1/README.md)
2. [Compromise Basic User and Enumerate Domain](./Step-2/README.md)
3. [Monitor and Detect!](./Step-3/README.md)

## 4 VMs
1. Kali Linux Attack Box
2. Windows 11 DC
3. Windows 10 Workstation
4. Ubuntu Box with Splunk Server


## Takeaways
The attack chain hit too many errors, so I stopped it midway and pivoted to detection and monitoring of the fails and successes. Detection mattered more than finishing the chain, and Splunk showed me clearly which attempts succeeded and which failed. Next time, I want more story context and more log noise so the investigation feels closer to a real incident.
