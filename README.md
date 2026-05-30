# SOC Threat Detection Lab
This lab demonstrates how to deploy a SIEM, capture endpoint telemetry, simulate real adversary techniques, and build detections mapped to the MITRE ATT&CK framework.

---------------------------------------------------------------------------------------------
#### Tools Used
- Splunk Enterprise
- Sysmon (Olafharttong config)
- Atomic Red Team
- PowerShell
- Windows 10 (VirtualBox VM)
- MITRE ATT&CK Framework
---------------------------------------------------------------------------------------------
#### Architecture
- Windows 10 VM running both Sysmon and Splunk
- Sysmon captures endpoint telemetry and forwards to Splunk via WinEventLog inpunt
- Aomic Red Team simulates adversary techniques on the same host
- Use SPL queries to detect simulated attacks
---------------------------------------------------------------------------------------------
#### Setup
###### Prerequisites
- VirtualBox Installed
- Windows 10 VM [8GB RAM, 4 CPUs, 50 GB Disk] (A lower Ram or CPU may affect the Splunk's response time causing lag)
- Internet access for the VM (NAT)
---------------------------------------------------------------------------------------------
#### Installation
Step 1 -- Download Splunk Enterprise
   - Inside your Windows 10 VM open a browser and go to:
     https://www.splunk.com/en_us/download/splunk-enterprise.html
   - Create a free account and download the .msi installer
   - Leave install path as default and create a User/Pass
   - Once complete verify splunk is running; Open browser and go to : http://localhost:8000
   - Login with you credential created and you should see Splunk home dashboard

Step 2 -- Install Sysmon
     Create a Tools folder and download Sysmon:
     https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
     - Unzip the download and extract content into the Tools folder e.g(C:\Tools\Sysmon)
     - Use PowerShell in Admin mode to dowload the olafhartong config file as it is less restrictive and better for lab use<img width="932" height="159" alt="Screenshot 2026-05-29 134445" src="https://github.com/user-attachments/assets/2977787c-2db0-46ec-954d-afefb3cee2dc" />



















     
