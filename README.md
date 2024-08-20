# SIEM
Building a SIEM
Created a new resource group named "SIEM_lab"
<img width="1481" alt="image" src="https://github.com/user-attachments/assets/85db9fac-242c-4e2a-8719-bbb26498fd8d">

I created a new firewall rule for the VM to allow any traffic inbound into the machine. This made the machine vulnerable and easily accessible by anyone, which gave us more things to analyze on our SIEM.
<img width="640" alt="image" src="https://github.com/user-attachments/assets/90388a93-bd16-4cfe-a702-3ba1e18d0661">

Deployed the VM
<img width="1507" alt="image" src="https://github.com/user-attachments/assets/b22f94d6-d881-4193-af8e-89fe266c7fe5">

Created a log analytics workspace
<img width="1506" alt="image" src="https://github.com/user-attachments/assets/ad4db591-cd04-4af2-ae98-20b1623fa7d3">

The tutorial asked to look for azure security center but it has now been rebranded to Microsoft Defender for Cloud. I found it and toggled the defender plan for the servers to be on since my VM is a server.

<img width="1507" alt="image" src="https://github.com/user-attachments/assets/fc905f36-8c77-401d-a9f0-f41a5ceb74f7">

Turned on data collection for all events
<img width="1506" alt="image" src="https://github.com/user-attachments/assets/cfa8c036-2499-4662-989f-bb4a9547582f">

Created Microsoft Sentinel and connected it to the workspace
<img width="1510" alt="image" src="https://github.com/user-attachments/assets/8d64e88a-677f-4b45-b631-eabfcfec6417">

Went to my VM and copied its public IP address 
<img width="1509" alt="image" src="https://github.com/user-attachments/assets/37ca3a33-8ad1-484b-98be-288c9ed7be15">

I used a Windows 11 VM on VMware to log in using RDP
<img width="1222" alt="image" src="https://github.com/user-attachments/assets/8285b027-9254-4d42-bbf7-f3a909cc929a">

Opened Eventviewer on the Azure Virtual machine, and opened the security logs
<img width="1510" alt="image" src="https://github.com/user-attachments/assets/818b578e-ab0f-4234-a006-68735a4eb568">

When I first tried to log in using RDP. i thought I was supposed to use my actual computer login but I was supposed to use the Azure credentials. So that was a failed login and I could see it in the Security logs
<img width="1464" alt="image" src="https://github.com/user-attachments/assets/65cff6e5-7717-4c72-9882-67dd93d7feb1">

While looking at the logs I also discovered that other people tried logging in. I can see failed login attempts at different times and from a different IP from mine
<img width="1367" alt="image" src="https://github.com/user-attachments/assets/f37bda9d-5f33-4bba-bcb8-42d8cb638667">

We are going to use the website "ipgeolocation" to look up the failed login attempts IP addresses and determine the location of the attackers

I tried pinging the Azure VM but i kept getting request timed out
<img width="996" alt="image" src="https://github.com/user-attachments/assets/22de5ec7-8460-4d08-87da-b44c230b79bb">

I went to went back on RDP and turned off the Windows Defender firewall 
<img width="1414" alt="image" src="https://github.com/user-attachments/assets/4fc4533e-b58b-4e80-9a29-a1e57ed6704f">

I went back and pinged the Azure VM again and this time I received a response
<img width="1118" alt="image" src="https://github.com/user-attachments/assets/db77a0f3-988a-4927-807f-6a43e2373297">
