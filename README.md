# SIEM Deployment on Azure

This project involves deploying a Security Information and Event Management (SIEM) system on Azure to monitor and analyze failed Remote Desktop Protocol (RDP) login attempts. The logs were collected by running a PowerShell ISE script that tracked all failed RDP logins from the Windows Event Manager. The script extracted IP addresses from these failed attempts and queried a website to determine the geographical location associated with each IP address. The information was then saved to a Notepad file on Windows, which was subsequently connected to Azure. This setup allowed for real-time visualization of failed login attempts on a map, providing insights into the countries and approximate areas from which these attempts originated.

## Project Overview

### Host and Virtual Machines

- **Host Computer**: Windows 11 VM running on VMware Workstation.
- **Azure VM**: Windows 10 Azure Virtual Machine (Intentionally Left Exposed for Security Testing)

The Windows 11 VM serves as the host computer, which is used to access and manage the Azure VM through Remote Desktop Protocol (RDP).

## Steps Involved

### 1. Resource Group Creation
- **Action**: Created a new resource group named `SIEM_lab` in Azure to organize and manage all resources related to this project.
![Resource Group](https://github.com/user-attachments/assets/85db9fac-242c-4e2a-8719-bbb26498fd8d)

### 2. Azure VM Deployment
- **Action**: Deployed a new virtual machine on Azure.
- **Firewall Configuration**: Configured the firewall to allow all inbound traffic, intentionally making the VM vulnerable to unauthorized access. This setup provides more data for analysis in the SIEM.
![Firewall Rule](https://github.com/user-attachments/assets/90388a93-bd16-4cfe-a702-3ba1e18d0661)
![VM Deployment]![image](https://github.com/user-attachments/assets/a1c6b525-fba2-4fd9-a29e-eefc1129a51d)


### 3. Log Analytics Workspace Creation
- **Action**: Created a Log Analytics Workspace in Azure to collect and analyze log data from the deployed VM.
![Log Analytics Workspace](https://github.com/user-attachments/assets/ad4db591-cd04-4af2-ae98-20b1623fa7d3)

### 4. Security Center Configuration
- **Action**: Enabled Microsoft Defender for Cloud for the VM to monitor and enhance its security posture.
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
![Unknown Login Attempts](https://github.com/user-attachments/assets/f37bda9d-5f33-4bba-bcb8-42d8cb638667)

### 9. Network Connectivity Testing
- **Action**: Initially attempted to ping the Azure VM from the Windows 11 host VM but received a 'Request Timed Out' response. After disabling the Windows Defender firewall on the Azure VM, the ping was successful, confirming network connectivity.
![Ping Attempt Failed](https://github.com/user-attachments/assets/22de5ec7-8460-4d08-87da-b44c230b79bb)
![Disabled Firewall](https://github.com/user-attachments/assets/4fc4533e-b58b-4e80-9a29-a1e57ed6704f)
![Ping Success](https://github.com/user-attachments/assets/db77a0f3-988a-4927-807f-6a43e2373297)

### 10. Script for Logging Failed RDP Attempts
- **Action**: Opened PowerShell ISE and added a script that monitors Windows Event Viewer for failed RDP login attempts and logs details such as the IP address, username, and timestamp. It then retrieves geolocation data for the source IP addresses using an external API and appends this information to a log file. The script continuously runs, updating the log with new events as they occur.
![PowerShell Script](https://github.com/user-attachments/assets/b003dd4b-5897-49a8-9f58-bc8d6dbf6e36)

I initially ran the script, but it was taking a while to see any new failed login attempts. I then modified the script to also display previous failed login attempts that had already occurred.
![Modified Script](https://github.com/user-attachments/assets/af52e7da-645b-4ad2-b155-265cb968cb86)

### 11. Troubleshooting Custom Logs
- **Troubleshooting**: The tutorial's method for creating custom logs was outdated, as I didn't see the option to add custom logs.
I went to add a data source, clicked on custom, and attempted to add the logs created from the script. Due to an error with data collection endpoints, I had to create a new Data Collection Endpoint. Initially, I encountered an error stating: "The subscription is not registered to use namespace 'microsoft.insights'." I resolved this by registering the 'microsoft.insights' namespace in my subscription.
![Register Subscription](https://github.com/user-attachments/assets/088b25d5-3abe-4b5b-b765-e9361969e858)

I was now able to create the data collection endpoint. I converted the failed RDP logs text file into JSON format. I then uploaded this file to create a custom log and used the table name 'failedrdp_CL', with the file pattern 'C:\programdata\failed_rdp.log' to monitor the log collection.
![Create Data Collection Endpoint](https://github.com/user-attachments/assets/f990c75b-6fd0-44cd-8836-26b510e07099)
![Log File Conversion](https://github.com/user-attachments/assets/adaa12dd-b6ea-4673-bdfe-6fc732f2ed72)
However, this didn't help because I couldn't add my Notepad file on the Azure VM, which was receiving the failed RDP logs, because the option to select it was not available.

**Resolution**: A Reddit post about a similar issue suggested using the Microsoft Monitoring Agent (MMA) instead of DCR-based custom logs. I followed this advice and specified the path where the logs are collected on the Azure VM.
![MMA-based Logs](https://github.com/user-attachments/assets/44388327-23fa-4ea6-8c01-b9c887577cc2)
![Log Path](https://github.com/user-attachments/assets/3a9810ba-54a6-41d5-8eb1-cbfd7e544c04)

### 12. Data Extraction and Visualization
- **Action**: Ran a query to extract fields from the raw data using the following KQL code:
```kql
Failedrdp_with_geo_CL
| extend latitude = extract("latitude:(.*?),", 1, RawData),
         longitude = extract("longitude:(.*?),", 1, RawData),
         destinationhost = extract("destinationhost:(.*?),", 1, RawData),
         username = extract("username:(.*?),", 1, RawData),
         sourcehost = extract("sourcehost:(.*?),", 1, RawData),
         state = extract("state:(.*?),", 1, RawData),
         country = extract("country:(.*?),", 1, RawData),
         label = extract("label:(.*?),", 1, RawData),
         timestamp = extract("

timestamp:(.*)", 1, RawData)
| project TimeGenerated, Computer,latitude, longitude, destinationhost, username, sourcehost, state, country, label, timestamp
```
![image](https://github.com/user-attachments/assets/e135964c-a5b0-4015-ac8f-f318658af050)


I created a Microsoft Sentinel workbook and visualized the data using a map. Changed the Metric label to 'label' and Metric Value to 'latitude'.
![Map Visualization](https://github.com/user-attachments/assets/8674776e-aeae-4e16-9c80-01b58330501e)
Changed the Metric Value to 'event count'.
![image](https://github.com/user-attachments/assets/846eb0c9-2b72-4213-80e2-97f6ff691398)


### 13. Analysis and Findings
- **Action**: Observed and analyzed the data from the map visualization. Initially, there were 237 failed attempts from the Netherlands, but after 17 hours, there were 17,000 failed attempts from Russia and 735 from the US, indicating potential unauthorized access attempts from various regions.
![Final Map](https://github.com/user-attachments/assets/5adba052-48dd-4380-bd1b-d0440e2134ff)

## Conclusion
I thoroughly enjoyed this project. Despite facing challenges due to outdated tutorial methods and errors in configuring custom logs, I was able to successfully deploy and configure the SIEM system on Azure. This experience significantly enhanced my problem-solving skills and provided valuable insights into real-world security monitoring and analysis.
