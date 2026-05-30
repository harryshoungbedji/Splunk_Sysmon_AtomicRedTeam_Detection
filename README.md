# SOC Threat Detection Lab
This lab demonstrates how to deploy a SIEM, capture endpoint telemetry, simulate real adversary techniques, and build detections mapped to the MITRE ATT&CK framework.

-------
### Tools Used
- Splunk Enterprise
- Sysmon (Olafharttong config)
- Atomic Red Team
- PowerShell
- Windows 10 (VirtualBox VM)
- MITRE ATT&CK Framework
-----
### Architecture
- Windows 10 VM running both Sysmon and Splunk
- Sysmon captures endpoint telemetry and forwards to Splunk via WinEventLog inpunt
- Aomic Red Team simulates adversary techniques on the same host
- Use SPL queries to detect simulated attacks
  
-----
### Setup
###### Prerequisites
- VirtualBox Installed
- Windows 10 VM [8GB RAM, 4 CPUs, 50 GB Disk] (A lower Ram or CPU may affect the Splunk's response time causing lag)
- Internet access for the VM (NAT)
  
-------
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

5. Open PowerShell as Administrator and download the olafhartong config file.This config is less restrictive than SwiftOnSecurity and better suited for lab use:
```powershell
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml" -OutFile "C:\Tools\sysmonconfig-lab.xml"
```
![Sysmon config download](https://github.com/user-attachments/assets/2977787c-2db0-46ec-954d-afefb3cee2dc)

5. Install Sysmon with the config"
```powershell
   cd C:\Tools\Sysmon
   .\Sysmon64.exe -accepteula -i C:\Tools\sysmonconfig-lab.xml
```
![Sysmon config download](https://github.com/user-attachments/assets/89396635-20b2-4885-8e88-eb149d70907a)

6. Verify Sysmon is running"
```powershel
   Get-Service Sysmon64
```
Status should say **Running**

7. Verify events are flowing - open Event Viewer and navigate to:
   *Applications and Services Logs → Microsoft → Windows → Sysmon → Operational

---

### Step 3  —  Connect Sysmon to Splunk
```powershel
@"
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = main
sourcetype = WinEventLog:Microsoft-Windows-Sysmon/Operational
disabled = false
start_from = oldest
current_only = 0
checkpointInterval = 5

[WinEventLog://Security]
index = main
sourcetype = WinEventLog:Security
disabled = false

[WinEventLog://System]
index = main
sourcetype = WinEventLog:System
disabled = false
"@ | Out-File -FilePath "C:\Program Files\Splunk\etc\system\local\inputs.conf" -Encoding UTF8
```

---
### Step 4  —  Install the splunk Add-on for Sysmon
1. Go to this URL inside your VM browser:
   ```url
   https://splunkbase.splunk.com/app/5709
   ```
2. Click Download, you'll need to be logged into your splunk account. It downloads as a `.tgz`
3. Navigate to the Apps menu (top left, gear icon next to "Apps", select "Manage")
 - Click Install app from file
 - Click Choose File → select the .tgz you just downloaded
 - Click Upload
![Sysmon config download](https://github.com/user-attachments/assets/95d8616d-9bfe-444c-868e-bf1e574c2d33)

---
### Step 5  —  Restart Splunk

   ```powershell
   & "C:\Program Files\Splunk\bin\splunk.exe" restart
   ```
![Sysmon config download](https://github.com/user-attachments/assets/a0d3d517-c1b7-4e39-92be-5e1b60993d1d)

---
### Step 6 —  Verify data is being ingested into splunk
Go to http://localhost:8000 → Search & Reporting → run this — set time to All Time:
```
index=main | stats count by sourcetype
```
![Sysmon config download](https://github.com/user-attachments/assets/7f352220-9200-4266-bdbf-dad43bf39733)

---
### Step 7 —  Verify EventID field extracts automatically
```
index=main sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational" | stats count by EventID | sort -count
```
![Sysmon config download](https://github.com/user-attachments/assets/0952ee38-1522-47b9-adc6-9ff5efb35226)
















