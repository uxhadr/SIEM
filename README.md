# SIEM Deployment on Azure

This project involves deploying a Security Information and Event Management (SIEM) system on Azure. The SIEM is designed to monitor and analyze security logs from various sources, helping to detect and respond to potential security threats in real time.

## Project Overview

### Host and Virtual Machines

- **Host Computer**: Windows 11 VM running on VMware Workstation.
- **Target VM**: Azure Virtual Machine (main focus of the project).

The Windows 11 VM serves as the host computer, which is used to access and manage the Azure VM through Remote Desktop Protocol (RDP).

## Steps Involved

### 1. Resource Group Creation
- **Action**: Created a new resource group named `SIEM_lab` in Azure to organize and manage all resources related to this project.
![Resource Group](https://github.com/user-attachments/assets/85db9fac-242c-4e2a-8719-bbb26498fd8d)

### 2. Azure VM Deployment
- **Action**: Deployed a new virtual machine on Azure.
- **Firewall Configuration**: Configured the firewall to allow all inbound traffic, intentionally making the VM vulnerable to unauthorized access. This setup provides more data for analysis in the SIEM.
![Firewall Rule](https://github.com/user-attachments/assets/90388a93-bd16-4cfe-a702-3ba1e18d0661)
![VM Deployment](https://github.com/user-attachments/assets/b22f94d6-d881-4193-af8e-89fe266c7fe5)

### 3. Log Analytics Workspace Creation
- **Action**: Created a Log Analytics Workspace in Azure to collect and analyze log data from the deployed VM.
![Log Analytics Workspace](https://github.com/user-attachments/assets/ad4db591-cd04-4af2-ae98-20b1623fa7d3)

### 4. Security Center Configuration
- **Action**: Enabled Microsoft Defender for Cloud (formerly Azure Security Center) for the VM to monitor and enhance its security posture.
![Microsoft Defender for Cloud](https://github.com/user-attachments/assets/fc905f36-8c77-401d-a9f0-f41a5ceb74f7)

### 5. Event Data Collection
- **Action**: Enabled data collection for all events to ensure comprehensive logging.
![Data Collection](https://github.com/user-attachments/assets/cfa8c036-2499-4662-989f-bb4a9547582f)

### 6. Microsoft Sentinel Integration
- **Action**: Created a Microsoft Sentinel instance and connected it to the Log Analytics Workspace for advanced threat detection and response.
![Microsoft Sentinel](https://github.com/user-attachments/assets/8d64e88a-677f-4b45-b631-eabfcfec6417)

### 7. Public IP Retrieval and RDP Access
- **Action**: Retrieved the public IP address of the Azure VM and used it to log in using Remote Desktop Protocol (RDP) from the Windows 11 host VM.
![Public IP Address](https://github.com/user-attachments/assets/37ca3a33-8ad1-484b-98be-288c9ed7be15)
![RDP Login](https://github.com/user-attachments/assets/8285b027-9254-4d42-bbf7-f3a909cc929a)

### 8. Security Log Analysis
- **Action**: Opened the Event Viewer on the Azure VM and accessed the Security logs. Observed my failed login attempt (due to incorrect credentials) and noted that it was logged in the Security logs. Also discovered failed login attempts from unknown IP addresses, indicating potential unauthorized access attempts.
![Security Logs](https://github.com/user-attachments/assets/818b578e-ab0f-4234-a006-68735a4eb568)
![Failed Login](https://github.com/user-attachments/assets/65cff6e5-7717-4c72-9882-67dd93d7feb1)
![Unknown Login Attempts](https://github.com/user-attachments/assets/f37bda9d-5f33-4bba-bcb8-42d8cb638667)

### 9. Network Connectivity Testing
- **Action**: Attempted to ping the Azure VM from the Windows 11 host VM but initially received a "Request Timed Out" response. Disabled the Windows Defender firewall on the Azure VM and successfully pinged the machine, confirming network connectivity.
![Ping Attempt Failed](https://github.com/user-attachments/assets/22de5ec7-8460-4d08-87da-b44c230b79bb)
![Disabled Firewall](https://github.com/user-attachments/assets/4fc4533e-b58b-4e80-9a29-a1e57ed6704f)
![Ping Success](https://github.com/user-attachments/assets/db77a0f3-988a-4927-807f-6a43e2373297)


I opened Powershell ISE and added a script that monitors Windows Event Viewer for failed RDP login attempts and logs details such as the IP address, username, and timestamp. It then retrieves geolocation data for the source IP addresses using an external API and appends this information to a log file. The script continuously runs, updating the log with new events as they occur.
<img width="1463" alt="image" src="https://github.com/user-attachments/assets/b003dd4b-5897-49a8-9f58-bc8d6dbf6e36">

I ran the script and I wasn't getting any new failed login attempts so I changed the script to also show old failed login attempts that had already been created
<img width="1418" alt="image" src="https://github.com/user-attachments/assets/af52e7da-645b-4ad2-b155-265cb968cb86">

Below is my process troubleshooting before I finally figured out how to add custom text logs
**Troubleshooting:** In the tutorial the creator was able to create custom logs in the log analytis workspace. Custom logs is not an option anymore so i had to use data collection rules in the agents tab. 
<img width="1501" alt="image" src="https://github.com/user-attachments/assets/9776b35f-635c-4cb9-a593-a2c146d8d9c1">

Went to add data source then I clicked on custom
Opened rdp and copied the logs created from the script we ran earlier
I wasn't able to add custom logs from the text file due to no data collection endpoint, so I created a new Data collection endpoint
It wouldn't let me create an endpoint and was giving me the following error: The subscription is not registered to use namespace 'microsoft.insights'. See https://aka.ms/rps-not-found for how to register subscriptions.
So I had to go to my subscription settings and register Microsoft.Inisgts
<img width="1502" alt="image" src="https://github.com/user-attachments/assets/088b25d5-3abe-4b5b-b765-e9361969e858">
I was now able to succesfully create my data collection endpoint
<img width="1467" alt="image" src="https://github.com/user-attachments/assets/f990c75b-6fd0-44cd-8836-26b510e07099">
I converted the failed rdp logs text file into Json and uploaded the file to create a custom log. This will train analytics on what to look for in the log file
<img width="1108" alt="image" src="https://github.com/user-attachments/assets/adaa12dd-b6ea-4673-bdfe-6fc732f2ed72">

I used a table name of a file I had already created, 'failedrdp_CL'. The File Pattern field in the Data Collection Rule configuration is used to identify which files on the VM should be monitored for log collection. I used the file pattern 'C:\programdata\failed_rdp.log' because that's where we stored our files earlier.
<img width="842" alt="image" src="https://github.com/user-attachments/assets/3047e053-d88e-4a7b-9f3a-74392acf27f2">


**This is after I finally saw a reddit post and realized I should have used MMA instead of DCR-based**
After alot of troubleshootning I finally found a reddit post that suggested to create custom log files you go to tables and select MMA-based instead of DCR-based.
<img width="1240" alt="image" src="https://github.com/user-attachments/assets/44388327-23fa-4ea6-8c01-b9c887577cc2">

Added the path to where the log is collected on the Azure VM because that is where our PowerShell script stored our logs
<img width="1145" alt="image" src="https://github.com/user-attachments/assets/3a9810ba-54a6-41d5-8eb1-cbfd7e544c04">

I ran the command in the logs query 
<img width="1198" alt="image" src="https://github.com/user-attachments/assets/a52610f7-2fd8-42b1-9b2d-fd9612e72c35">

I extra the fields from the raw data to  reate their own fields using the followin code:
'Failedrdp_with_geo_CL
| extend latitude = extract("latitude:(.*?),", 1, RawData),
         longitude = extract("longitude:(.*?),", 1, RawData),
         destinationhost = extract("destinationhost:(.*?),", 1, RawData),
         username = extract("username:(.*?),", 1, RawData),
         sourcehost = extract("sourcehost:(.*?),", 1, RawData),
         state = extract("state:(.*?),", 1, RawData),
         country = extract("country:(.*?),", 1, RawData),
         label = extract("label:(.*?),", 1, RawData),
         timestamp = extract("timestamp:(.*)", 1, RawData)
| project TimeGenerated, Computer,latitude, longitude, destinationhost, username, sourcehost, state, country, label, timestamp'

This is the output I received:
<img width="962" alt="image" src="https://github.com/user-attachments/assets/138a7584-eee6-4ba2-93fa-4b4a6be83edf">

Went to Microsoft Sentinel and created a workbook. Used the code from earlier to create the workbook and added the following code: '| summarize event_count = count() by sourcehost, latitude, longitude, country, label, destinationhost
| where destinationhost != "samplehost" 
| where sourcehost != "" '

I changed the visualization to view by map. In the map settings I change the Metric label to label, and the Metric Value to latitude.
<img width="1473" alt="image" src="https://github.com/user-attachments/assets/8674776e-aeae-4e16-9c80-01b58330501e">

Changed the Metric Value to event count to see the number of events based on each country
<img width="1465" alt="image" src="https://github.com/user-attachments/assets/9e00093f-8386-43a6-b2c3-29e8b7083d32">

I saved the map and named it 'FailedRDP World Map'.
It's currently 1AM, and most of the attempts (237 attempts) are coming from the Netherlands. I will leave it running and come back tomorrow to see the progress.

I came back 17 hours later and I was surprised with my findings. Earlier I didn't have any attempts from Russia but I now had 17,000 failed RDP attempts from Russia, and 735 from the US, and Netherlands was still at 237 failed attempts.
<img width="1033" alt="image" src="https://github.com/user-attachments/assets/5adba052-48dd-4380-bd1b-d0440e2134ff">

I went to Microsoft defender and saw attempted but failed dictionary attacks
<img width="1198" alt="image" src="https://github.com/user-attachments/assets/2d8513b1-b691-4d10-9288-5de521b42ef3">


Conclusion: I loved doing this project. At first I looked at the comments of the tutorial since the video is 2yrs old, and a lot of them were complaining how it was almost impossible to do this project, but I also so a few that stated how they were able to figure it out so I decided to take on the task. I encountered so many problems, which forced me to practice my problem-solving skills. It was frustrating trying to figure out some stuff but it felt so good after I finally figured out the solution to a problem.



