## Steps Taken Pre-SIEM-Base-Installation

1. Downloaded and installed Kali Linux (VirtualBox pre-built image) — confirmed all tools present
2. Set up the VirtualBox Host-only Network (192.168.56.1/24, DHCP enabled) — discovered it already existed by default
3. Built the Ubuntu Server VM — downloaded ISO, created VM (1GB RAM, 1 CPU, 20GB disk), installed, attached to Host-only Adapter
4. Verified Ubuntu — logged in, ran `ip a` to get its IP, confirmed reachable via ping from Kali
5. Downloaded Windows Server 2025 ISO from Microsoft Evaluation Center
6. Created the Windows Server VM (DC01) — 4GB RAM, 2 CPU cores, 40-50GB dynamic disk
7. Troubleshot missing Host-only Adapter option (needed Expert mode instead of Basic)
8. Fixed a "VM failed to boot" error caused by the ISO not being attached to the optical drive
9. Installed Windows Server 2025 Standard (Desktop Experience), set Administrator password
10. Installed the AD DS role via Server Manager
11. Promoted the server to a Domain Controller — new forest, domain name `lab.local`
12. Confirmed AD DS working via Active Directory Users and Computers showing `lab.local`
13. Created an Organizational Unit ("LabUsers") and two test user accounts
14. Downloaded Windows 10 ISO via Media Creation Tool
15. Created the Windows 10 VM (Win10-Workstation) — 3GB RAM, 2 CPU cores, 40GB disk, attached to Host-only Adapter
16. Installed Windows 10, landed at desktop with a local account
17. Set Windows 10's DNS to point manually at the DC's IP address, in preparation for domain join
18. Troubleshot `ping lab.local` not resolving — confirmed both IP and domain ping work once both VMs are powered on
19. Discovered Windows 10 was Home edition (can't join a domain) — upgraded to Pro in-place using a generic KMS key
20. Successfully joined Windows 10 to the `lab.local` domain (after learning the DC must be powered on for the join to succeed)
21. Installed Sysmon (with SwiftOnSecurity's config) on Windows 10 — used a temporary NAT adapter to download files, then reverted to host-only
22. Troubleshot Sysmon logging — fixed an "Access is denied" error in Event Viewer by running it as Administrator
23. Installed Sysmon the same way on the Windows Server DC
24. Decided on Splunk (over Elastic) as your SIEM, given RAM constraints — found TryHackMe's "Splunk: Setting up a SOC Lab" room to follow (you have premium)
25. Planned to bump Ubuntu's resources (to ~2-4GB/2 cores) to host Splunk
26. Forgot your Linux passwords — reset Ubuntu's password via GRUB recovery mode
27. Troubleshot Ubuntu hanging on `systemd-networkd-wait-online.service` at boot — resolved after waiting it out
28. Noted current IP addresses for all four VMs for future reference
29. **After all this, 4 VMs are built, networked, sysmon acquired, domain / AD setup. Installing Splunk is next in combination with the THM room.**

## Steps Taken for SIEM-Base-Installation

1. Bumped Ubuntu's resources to 3000 MB RAM / 2 CPU cores to host Splunk
2. Downloaded the Splunk Enterprise .deb package via `wget` (typed the URL manually after clipboard sharing didn't work)
3. Installed Splunk with `dpkg -i` — confirmed `/opt/splunk` populated correctly despite a harmless warning
4. Started Splunk, hit a "running as root is deprecated" warning, resolved with `-run-as-root` flag
5. Got the "Splunk web interface is at http://Ubuntu-Target:8000" success message
6. Troubleshot Splunk not being reachable after a reboot — discovered Splunk doesn't auto-start after VM restart, restarted it manually
7. Confirmed Ubuntu's firewall (`ufw`) was inactive, ruling that out as the blocker
8. Successfully accessed the Splunk web UI from your host browser at `http://192.168.56.101:8000`

## Steps Taken for Post-SIEM-Base-Installation

1. Configured Splunk to receive forwarded data — Settings → Forwarding and receiving → Configure receiving → added port 9997
2. Created a dedicated index named "windows" (Settings → Indexes → New Index) to organize Windows log data separately from the default "main" index
3. Downloaded the Splunk Universal Forwarder (Windows 64-bit .msi) on the DC via the NAT-adapter trick, using a direct download link pulled from Splunk's page source (`view-source` → search `data-link`) to avoid a slow web login
4. Installed the Universal Forwarder on the DC — set Receiving Indexer to `192.168.56.101:9997`, set forwarder credentials
5. Repeated the same Universal Forwarder download/install process on the Windows 10 workstation
6. Reverted both DC and Windows 10 back to host-only-only networking after each download
7. Attempted the "Add Data → Forward" wizard in Splunk's web UI to configure log inputs — found this requires a Deployment Server (not set up), so it showed nothing to configure
8. Chose the manual configuration method instead — created `inputs.conf` directly in `C:\Program Files\SplunkUniversalForwarder\etc\system\local\` on both the DC and Windows 10, defining WinEventLog inputs for Security, System, and Microsoft-Windows-Sysmon/Operational
9. Enabled "File name extensions" in File Explorer to confirm the config file saved correctly as `inputs.conf` (not `inputs.conf.txt`)
10. Restarted the `SplunkForwarder` service on both machines (`Restart-Service SplunkForwarder` via elevated PowerShell) to apply the new inputs
11. Verified connectivity with `Test-NetConnection -ComputerName 192.168.56.101 -Port 9997` on both forwarders — confirmed `TcpTestSucceeded : True`
12. Confirmed data flowing into Splunk via `index=* | stats count by host` in Search & Reporting
13. Discovered logs were landing in the default "main" index rather than the custom "windows" index (likely a config mismatch) — confirmed functionally fine for search/detection purposes, noted as a cleanup item for later
14. **End state**: Sysmon → Universal Forwarder → Splunk pipeline fully working end-to-end, searchable via `index=main`
