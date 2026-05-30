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
1. Inside your Windows 10 VM open a browser and go to:
   https://www.splunk.com/en_us/download/splunk-enterprise.html
