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
