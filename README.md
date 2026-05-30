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
--------------------------------------------------------------------------------------------
## Installation

### Step 1 — Download Splunk Enterprise

1. Inside your Windows 10 VM open a browser and go to:
```url
https://www.splunk.com/en_us/download/splunk-enterprise.html
```
2. Create a free account and download the Windows `.msi` installer
3. Run the installer as Administrator — leave the install path as default and create a username and password
4. Write down your credentials — you will need them constantly
5. Once complete verify Splunk is running by opening a browser and going to:
```url
http://localhost:8000
```
6. Log in with your credentials — you should see the Splunk home dashboard

---

### Step 2 — Install Sysmon

1. Create a Tools folder:
```powershell
   mkdir C:\Tools
```
2. Download Sysmon from:
```url
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
```
3. Unzip the download and extract contents into the Tools folder:
C:\Tools\Sysmon
4. Open PowerShell as Administrator and download the olafhartong config file.This config is less restrictive than SwiftOnSecurity and better suited for lab use:
```powershell
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml" -OutFile "C:\Tools\sysmonconfig-lab.xml"
```
![Sysmon config download](https://github.com/user-attachments/assets/2977787c-2db0-46ec-954d-afefb3cee2dc)
<img width="932" height="159" alt="Screenshot 2026-05-29 134445" src="https://github.com/user-attachments/assets/1a1332f1-cc29-4aa8-8532-452ae4ea9c60" />

5. Install Sysmon with the config"
```powershell
   cd C:\Tools\Sysmon
   .\Sysmon64.exe -accepteula -i C:\Tools\sysmonconfig-lab.xml
```
<img width="870" height="323" alt="Screenshot 2026-05-29 134650" src="https://github.com/user-attachments/assets/89396635-20b2-4885-8e88-eb149d70907a" />


6. Verify Sysmon is running"
```powershel
   Get-Service Sysmon64
```
  Status should say **Running**

7. Verify events are flowing - open Event Viewer and navigate to:
Applications and Services Logs → Microsoft → Windows → Sysmon → Operational
