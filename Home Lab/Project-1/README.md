# Attack, Defend, Remediate, Test Remediation - Project

0.5. [Confirming that Splunk Server & Forwarders are working](./Step%200.5/README.md)
1. [Brute Force Attack with Kali Linux](./Step%201/README.md)
2. [Investigate it in Splunk](./Step%202/README.md)
3. [Implement IT Support / Help Desk Remediation, Test Remediation after](./Step%203/README.md)

## 4 VMs
1. Kali Linux Attack Box
2. Windows 11 DC
3. Windows 10 Victim Box
4. Ubuntu Linux Victim Box


## Read [README2.md](./README2.md) for every step taken prior for my Home Lab setup which includes the VMs, Splunk Server, Forwarders, Sysmon, etc

## Takeaways
1. There are many options for attackers. Attackers look for open doors and can try any of them to get through. However, there's always a way to monitor and detect those open doors as a defender!
2. What cannot be applied to the real world, is having only once machine to monitor, without story context. Too easy.
3. GPO Management is not too difficult after understanding the hierarchy. I bet it'll be more difficult to find a very specific policy that I may have to configure.
4. I should remember that this is a friendly local attack, which can serve differently than attacks on the internet, but I'm sure the underlying fundamentals are the same. 



## Improvements for Next Project
1. Story Context
2. More logs (more noise)
