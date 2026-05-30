# SOC Threat Detection Lab

This lab demonstrates how to deploy a SIEM, capture endpoint telemetry, 
simulate real adversary techniques, and build detections mapped to the 
MITRE ATT&CK framework.

---

## Tools Used
| Tool | Purpose |
|------|---------|
| Splunk Enterprise | SIEM — log ingestion and detection |
| Sysmon (olafhartong config) | Endpoint telemetry capture |
| Atomic Red Team | Adversary technique simulation |
| PowerShell | Configuration and test execution |
| Windows 10 (VirtualBox) | Lab environment |
| MITRE ATT&CK Framework | Detection mapping |

---

## Architecture
- Windows 10 VM running both Sysmon and Splunk on the same host
- Sysmon captures endpoint telemetry and forwards to Splunk via WinEventLog input
- Atomic Red Team simulates adversary techniques on the same host
- SPL queries detect each simulated attack in Splunk

---

## Prerequisites
- VirtualBox installed on host machine
- Windows 10 VM — 8GB RAM, 4 CPUs, 50GB Disk
  > Note: Lower RAM or CPU will work but may cause Splunk to run slowly
- Internet access for the VM (NAT adapter)

---

## Installation

### Part 1 — Install Splunk Enterprise

1. Inside your Windows 10 VM open a browser and go to:
https://www.splunk.com/en_us/download/splunk-enterprise.html
2. Create a free account and download the Windows `.msi` installer
3. Run the installer as Administrator — leave the install path as default
4. Set a username and password — write them down, you will need them constantly
5. Verify Splunk is running by opening a browser and navigating to:
http://localhost:8000
6. Log in with your credentials — you should see the Splunk home dashboard

---

### Part 2 — Install Sysmon
#### Step 1
1. Open PowerShell as Administrator and create a Tools folder:
```powershell
   mkdir C:\Tools
```
2. Download Sysmon from Microsoft Sysinternals:
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
3. Unzip the download and extract contents to:
C:\Tools\Sysmon
4. Download the olafhartong config — this config is less restrictive than
   SwiftOnSecurity and better suited for lab use:
```powershell
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml" -OutFile "C:\Tools\sysmonconfig-lab.xml"
```
   ![Downloading olafhartong config](https://github.com/user-attachments/assets/2977787c-2db0-46ec-954d-afefb3cee2dc)

5. Install Sysmon with the config:
```powershell
   cd C:\Tools\Sysmon
   .\Sysmon64.exe -accepteula -i C:\Tools\sysmonconfig-lab.xml
```
   ![Installing Sysmon](https://github.com/user-attachments/assets/89396635-20b2-4885-8e88-eb149d70907a)

6. Verify Sysmon is running:
```powershell
   Get-Service Sysmon64
```
   Status should say **Running**

7. Verify events are flowing — open Event Viewer and navigate to:
Applications and Services Logs → Microsoft → Windows → Sysmon → Operational
   You should see events populating immediately

---

#### Step 2 — Connect Sysmon to Splunk

Run this entire block in Admin PowerShell to create inputs.conf:
```powershell
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

#### Step 3 — Install Splunk Add-on for Sysmon

1. Go to this URL inside your VM browser:
https://splunkbase.splunk.com/app/5709
2. Click **Download** — you must be logged into your Splunk account
   The file downloads as a `.tgz`
3. In Splunk at `http://localhost:8000`:
   - Click the gear icon next to **Apps** → select **Manage Apps**
   - Click **Install app from file**
   - Click **Choose File** → select the `.tgz` you downloaded
   - Click **Upload**

   ![Installing Sysmon Add-on](https://github.com/user-attachments/assets/95d8616d-9bfe-444c-868e-bf1e574c2d33)

---

#### Step 4 — Restart Splunk

```powershell
& "C:\Program Files\Splunk\bin\splunk.exe" restart
```
![Restarting Splunk](https://github.com/user-attachments/assets/a0d3d517-c1b7-4e39-92be-5e1b60993d1d)

---

### Step 6 — Verify Data Ingestion

Go to `http://localhost:8000` → Search & Reporting → set time to **All Time** and run:
```
index=main | stats count by sourcetype
```
You should see `WinEventLog:Microsoft-Windows-Sysmon/Operational` with a count
in the hundreds or thousands

![Verifying data ingestion](https://github.com/user-attachments/assets/7f352220-9200-4266-bdbf-dad43bf39733)

---

#### Step 7 — Verify EventID Field Extraction
```
index=main sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| stats count by EventID
| sort -count
```
You should see a clean table with EventID numbers like 1, 3, 7, 10, 11, 13.
If you see results here the setup is complete and working correctly.

![EventID field extraction](https://github.com/user-attachments/assets/0952ee38-1522-47b9-adc6-9ff5efb35226)

---

### Part 3 — Install Atomic Red Team
####Step 1 — Disable Windows Defender
####Step 2 —  Set execution policy
```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
```
####Step 3 — Install the framework
```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
```
####Step 4 —  Install the atomics folder
```powershell
Install-AtomicRedTeam -getAtomics -Force -InstallPath "C:\AtomicRedTeam"
```
####Step 5 — Import the module
```powershell
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
```
####Step 6 — Install ProcDump
```powershell
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Procdump.zip" -OutFile "C:\Tools\Procdump.zip"

Expand-Archive C:\Tools\Procdump.zip -DestinationPath C:\Tools\Procdump

New-Item -ItemType Directory "C:\AtomicRedTeam\ExternalPayloads" -Force

Copy-Item "C:\Tools\Procdump\procdump64.exe" "C:\AtomicRedTeam\ExternalPayloads\procdump.exe"
```
####Step 7 — Verify Atomic Red Team works
```powershell
Invoke-AtomicTest T1082 -ShowDetailsBrief
```
You should see a list of test names for T1082
![Verifying Atomic Works](https://github.com/user-attachments/assets/a305e9cf-9a93-4967-821f-1984661566d9)

---

### Part 3 — Install Atomic Red Team
