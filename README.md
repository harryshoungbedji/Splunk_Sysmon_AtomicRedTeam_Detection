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
## Installation

### Step 1 — Download Splunk Enterprise

1. Inside your Windows 10 VM open a browser and go to:
https://www.splunk.com/en_us/download/splunk-enterprise.html
2. Create a free account and download the Windows `.msi` installer
3. Run the installer as Administrator — leave the install path as default and create a username and password
4. Write down your credentials — you will need them constantly
5. Once complete verify Splunk is running by opening a browser and going to:
http://localhost:8000
6. Log in with your credentials — you should see the Splunk home dashboard

---

### Step 2 — Install Sysmon

1. Create a Tools folder:
```powershell
   mkdir C:\Tools
```
2. Download Sysmon from:
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
3. Unzip the download and extract contents into the Tools folder:












     
