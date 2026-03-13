# 🖥️ Active Directory Lab

A hands-on cybersecurity home lab simulating a small Active Directory environment 
with attack simulation and log analysis using Splunk.

## 📌 Overview

This lab was built to take my knowledge beyond theory and into practice. Using 
VirtualBox, I set up a small Active Directory infrastructure from scratch and used 
it to simulate real world security scenarios, from brute force attacks to log 
analysis and threat detection.

The environment consists of four virtual machines: a Windows 10 target machine, 
a Windows Server acting as the domain controller, a Kali Linux attacker machine, 
and an Ubuntu Server running Splunk for telemetry and log analysis. Together they 
gave me a full picture of both the offensive and defensive sides of cybersecurity.

## 🛠️ Tools & Environment

| Tool | Purpose |
|------|---------|
| Oracle VM VirtualBox | Creating and managing virtual machines |
| Windows Server 2022 | Active Directory Domain Controller |
| Windows 10 | Target machine |
| Kali Linux | Attacker machine for simulating brute force attacks |
| Ubuntu Server | Splunk server for log collection and analysis |
| Active Directory | Centralized domain management and user authentication |
| Splunk | SIEM, log analysis and threat detection |
| Splunk Universal Forwarder | Collecting and forwarding logs to Splunk |
| Sysmon | Endpoint monitoring and logging |
| Crowbar | Simulating brute force attacks via RDP |
| Atomic Red Team | Simulating adversary tactics and testing defenses |
| PowerShell | Scripting and automation |

## 💡 Key Takeaways

- Built and configured a fully functional Active Directory environment from scratch
- Gained hands on experience with both offensive and defensive security tools
- Learned how brute force attacks appear in log data and how to identify indicators of compromise
- Understood the value of a SIEM in detecting and investigating suspicious activity
- Discovered how tools like Atomic Red Team expose visibility gaps in your defenses
- Troubleshot real configuration issues that tutorials do not prepare you for, 
  including static IP persistence and network configuration across multiple VMs

## 📖 Full Step by Step Guide

## Phase 1: Environment Install and Configuration

***1. Draw a diagram of the environment using a tool such as <a href="https://app.diagrams.net/">draw.io</a>***

A lab diagram helps organize and plan the setup, showing how different systems, tools, and network elements are connected. This aids in troubleshooting, understanding workflows, and ensuring everything is properly configured for experiments or security testing.

![AD Lab Diagram](https://github.com/user-attachments/assets/d0d1021c-fe93-40a5-9367-1aef43656720)

***2. Install Oracle VM VirtualBox Manager***

- Download the compatible version from <a href="https://www.virtualbox.org/">VirtualBox</a>

***3. Install Windows 10***
- Navigate to <a href="https://www.microsoft.com/en-ca/software-download/windows10ISO">Microsoft</a> to install the compatible version.
   - Select the edition
   - Select the product language
   - Select 64-bit Download
   - Once the download pop up appears, select "Create installation media (USB flash drive, DVD, or ISO file) for another PC"
   - Select "ISO file" when pop up asks "Choose which media to use"
   - Save ISO file
- In VirtualBox, click "New" to create a new VM
   - Name: choose a name for VM (I named it Windows)

        Please note: this machine will be used as our target machine
   - Folder: select where you want VM to live
   - ISO Image: select ISO image that you just downloaded
   - Check "Skip Unattended Installation" to install OS manually

  ![Screen Shot 2025-02-04 at 5 15 38 PM](https://github.com/user-attachments/assets/d123ae11-c032-4ebc-baa8-40559718ec41)
  
  - Configure VM specifications (this can vary depending on your computer's specifications):
    - Select 4000 MB RAM for base memory, 1 CPU for processors, 50 GB for virtual hard disk
    - Finish
            
- Start VM and follow the installation prompts
   - Select "I don't have a product key"
   - Select "Windows 10 Pro"
   - Accept the license terms
   - Select "Custom: Install Windows only (advanced)"
   - Select "Next" to allow Windows to install 
      
***4. Install Kali Linux***

- Download <a href="https://www.kali.org/">Kali Linux</a>

   - Select the Pre-Built Virtual Machine
   - Choose either 64-bit (preferred) or 32-bit depending on your machine
     - To verify your machines specifications, select Windows key > type "System" > select "System Information" > view "System Type"
   - Click 64-bit, then choose VirtualBox 64
- Extract Kali Linux
   If you have any trouble extracting Kali Linux, download <a href="https://www.7-zip.org/">7 Zip</a> and extract with this tool.
- Once extracted, double click on the "vbox" file so that it's automatically imported into VirtualBox

![Screen Shot 2025-02-04 at 5 40 31 PM 1](https://github.com/user-attachments/assets/589bad71-9034-4702-955e-f566caea0a4f)

- Now, go to VirtualBox and run the Kali VM by clicking "Start"
- Log in with default credentials: kali/kali

***5. Install Windows Server***

- Download <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022">Windows Server 2022</a>
   - Fill out the form
   - Select "64-bit edition" under "ISO downloads"
     
- Once downloaded, go to VirtualBox and select "New" to create a new VM
   - Name: choose a name for VM (I named it ADDC since we will be using this as our Active Directory Domain Controller)
   - Folder: select where you want VM to live
   - ISO Image: select ISO image that you just downloaded
   - Check "Skip Unattended Installation" to install OS manually
     
- Configure VM specifications (this can vary depending on your computer's specifications):
   - Select 4000 MB RAM for base memory, 1 CPU for processors, 50 GB for virtual hard disk
   - Finish
     
- Start VM and follow the installation prompts
   - Select "Install Now"
   - Select "Windows Server 2022 Standard Evaluation (Desktop Experience)"
   - Accept the license terms
   - Select "Custom: Install Windows Microsoft Server Operating System only (advanced)"
   - Select "Next" to allow Windows to install
   - Once setup is completed, you will be prompted to create a password
   - Select "Finish"
     
- Log in with recently created password
      - Select "No" when asked "Do you want to allow your PC to be discoverable by oth PCs and devices on this network?"


***6. Install Ubuntu Server***

- Download <a href="https://ubuntu.com/server">Ubuntu Server</a>

- Once downloaded, go to VirtualBox and select "New" to create a new VM
   - Name: choose a name for VM (I named it Splunk since we will be using this as our Splunk Server)
   - Folder: select where you want VM to live
   - ISO Image: select ISO image that you just downloaded
   - Check "Skip Unattended Installation" to install OS manually
     
- Configure VM specifications (this can vary depending on your computer's specifications):
       - Select 8000 MB RAM for base memory, 2 CPU for processors, 100 GB for virtual hard disk
          - Splunk will require more specs as it will be ingesting data and we'll be running searches on it  
- Finish
  
- Start the VM
   - Select "Try or Install Ubuntu Server"
   - Follow installation prompts; leave as default
   - Fill out "Profile Setup" form, which is where you will choose your credentials
    - Once installed, select "Reboot Now"
    - Click "Enter" when "Failed unmounting" error pops up
      
- Once rebooted, logon with recently created credentials
    - run command: sudo apt-get update && sudo apt-get upgrade -y
         - this command will update & upgrade all of our repositories
    - Once completed, hit "Enter" 

***Summary***

You should now have Oracle VM VirtualBox Manager installed, along with four VMs running Windows 10, Kali Linux, Windows Server, and Splunk Server.

![lab set up](https://github.com/user-attachments/assets/07ac8df9-b70f-4338-8727-55a09dd50412)


## Phase 2: Configure the Network

***1. Setup Communications***

- In Virtual Box, navigate to Tools > Network > NAT Networks > Create
   - Refer to screenshot below for configuration details.

![NAT](https://github.com/user-attachments/assets/6a33e514-410c-4b33-b234-18148c97bb0b)

   - Click "Apply"
   - Navigate to each VM > Settings > Network
   - Refer to screenshot below for configuration details.

![NAT config](https://github.com/user-attachments/assets/90bdf6cc-0d5e-4c84-90c3-9c53c53230e9)

- Repeat the above 2 steps for each VM
   - Navigate to each VM > Settings > Network
   - Refer to screenshot above for configuration details.    
- Next, start Splunk VM
   - run: ip a
      - this will display current IP address, which will need to be updated to static IP 192.168.10.10/24
- To set a static IP on Splunk server:
   - Run: sudo nano /etc/netplan/00-installer-config.yaml
      - tab to auto-complete, should only have one file 
   - Configure:
  ```
  network: 
     ethernet:
       enp0s3:
         dhcp4: no
         addresses: [192.168.10.10/24]
         nameservers:
               addresses: [8.8.8.8]
         routes:
          - to: default
            via: 192.168.10.1
   version: 2
    ```
   - Save configuration
   - Run: ``` sudo netplan apply ```
   - Run: ``` ip a ```
      - This will verify if IP address persists and is set to 192.168.10.10/24   
   - Reboot
   - Verify connection by running: ping google.com
     
*The steps above did not work for me as I had a different configuration file, but this turned out to be a great opportunity to troubleshoot and learn more. The configuration file on my Splunk server was 50-cloud-init.yaml, meaning our IP updates wouldn’t persist after a reboot. To resolve this, I created a custom 00-installer-config.yaml file inside /etc/netplan/ and configured it with the updated IP. This ensured the static IP would remain after a reboot instead of reverting to DHCP. I also had to back up and delete the old 50-cloud-init.yaml file. Reboot and verify IP address once done.*

***2. Install Splunk***
   
- Navigate to <a href="https://www.splunk.com/">Splunk</a> download free trial of Splunk Enterprise
   - Select Linux as OS
   - Select ".deb" file
   - Save into preferred directory
- Navigate back to Splunk VM
   - Run: ``` sudo apt-get install virtualbox ```
      - this will display what options are available
   - Run: ``` sudo apt-get install virtualbox-guest-additions-iso ```
   - Type "y"
   - Click "Enter"
   - Once installed, run: clear
- Navigate to Devices (on the top left of the screen) > Shared Folders > Shared Folders Settings
   - Add a folder
      - Folder Path: select path where we saved our Splunk installer
      - Foler Name: leave as default (AD-Project in our case)
      - Select: Read-only, Auto-mount, and Make Permenant
      - Click "Ok" 
- In Splunk, run: sudo reboot
   - Once rebooted, log back in
   - Run: sudo adduser <your username> vboxsf
      - ex. sudo adduser username vboxsf
   - If you receive a "group vboxsf does not exist" error, we may need some additional guest installations
      - Run: sudo apt-get install virtualbox
         - This will display what options are available
      - Run: ```sudo apt-get install virtualbox-guest-utils```
      - Run: ```sudo reboot```
      - Run: ```sudo adduser username vboxsf```
         - User should be added to group now
- Create a new directory called "Share"
   - Run: ```mkdir Share```
   - Run: ```ls```
      - Newly created directory "Share" should be listed

![share directory](https://github.com/user-attachments/assets/40e15bb1-c81d-4024-a6ca-c805f11b71a6)

- Next, mount our shared folder onto our directory called "Share"
   - Run: sudo mount -t vboxsf -o uid=1000,gid=1000 <directory name where .deb file is located> share/
      - ex. sudo mount -t vboxsf -o uid=1000,gid=1000 AD-Project share/
      - If you forget what your shared folder name is:
         - Navigate to Devices > Shared Folders > Shared Folders Settings
   - If you get an error, exit session and re-do steps
      - Run: ```exit```
      - Error may occur because when you add your user into a new group, it may not reflect until you log out
   - Run: ```ls -la```
      - Should now see that our "share" is now highlighted

![highlighted share in splunk](https://github.com/user-attachments/assets/3280e379-abc0-4cd2-a231-ac85a8072c59)

- Change directories into that share
   - Run: ```cd share```
   - Run: ```clear```
   - Run: ```ls -la```
      - This will display all the files listed in this directory, including our Splunk Installer
- To install Splunk:
   - Run: sudo dpkg -i splunk (hit tab to auto-complete)
   - Once you see "Complete", should be good to change into directory where Splunk is located onto our server
- Change directory
   - Run: ```cd /opt/splunk```
   - Run: ```ls -la```
      - Here you will notice that all users and groups belong to Splunk, which is a good thing as it limits the permissions to that user
    
![cd :opt:splunk](https://github.com/user-attachments/assets/3eff4d56-be78-4771-b914-acf3e478259f)

- Change into user Splunk
   - Run: ```sudo -u splunk bash```
      - Now, we are acting as user Splunk 
- Run: ```cd bin```
   - Files listed in here are all binaries that Splunk can use
   - the one that we will use is "./splunk start"
   - Run: ```./splunk start```
      - This will run the installer
   - Click "q"
   - Type "y" to accept
   - Select an administrator username and password
- Once installation is completed:
   - Run: ```exit```
      - To exit out of user Splunk 
   - Run:```cd bin```
   - Run:``` sudo ./splunk enable boot-start -user splunk```
      - Whenever the VM reboots, Splunk will run with user splunk

![splnk login](https://github.com/user-attachments/assets/647ecc3c-8549-4d9b-ac4f-1ec626b56d54)
  
***3. Configure Windows Machine***

Please note that configuring the Windows Machine and the Windows Server is extremely similar. The main difference is that the Windows Machine will be renamed to "target-PC", meanwhile the Windows Server will be renamed to "ADDC01". Additionally, the Windows Machine's static IP address will be updated to 192.168.10.100, and the Windows Server's static IP will be updated to 192.168.10.7.
   
- Rename the host name of machine
   - In the Start Menu search, search "PC"
   - Select "Properties"
   - Select "Rename this PC"
   - Rename to "target-PC"
   - Select "Next"
   - Select "Restart Now"
     
- Update static IP to 192.168.10.100
   - Open command prompt
   - Run: ```ipconfig```
      - this will display current IPv4
   - Navigate to network icon at the bottom right of the window
   - Select "Open Network & Internet Settings"
   - Select "Change adapter options"
   - Right click the adapter, Ethernet
   - Select "Properties"
   - Select "Internet Protocol Version 4 (TCP/IPv4)" > "Properties"
   - Select "Use the following IP address"
   - Configure as shown below:
     
![windows 8 8 8 8](https://github.com/user-attachments/assets/84f4a826-b0e2-46d8-b099-59dac9e59256)


   - Run: ``` ipconfig to view updated IPv4```

![cmnd prompt](https://github.com/user-attachments/assets/38e0c671-84ff-4246-b9e3-f668b4fc1809)

- Install Splunk Universal Forwarder on target-PC (Please note: installing Splunk Universal Forwarder and Sysmon on the ADDC VM will follow these exact same steps)
   - Run Splunk VM (the following steps will not work if VM is not running)
   - In the target-PC, open the browser and type 192.168.10.10:8000
      - Splunk listens on port 8000 
   - In the target-PC, visit <a href="https://www.splunk.com/">Splunk</a>
      - Create an account
      - Select "Products"
      - Select "Free Trials & Downloads"
      -  Scroll down to Universal Forwarder & select "Get My Free Download"
      -  Select the compatible OS
   - Once download is completed, double click the msi file
      - Select "Check this box to accept the License Agreemnent"
      - Select "An on-premises Splunk Enterprise Instance"
      - Select "Next"
      - Choose your credentials
      - For "Receiving Indexer", this is going to be our Splunk Server's IP address
         - Hostname or IP: 192.168.10.10
         - Default port for Splunk when receiving events is: 9997
      - Select "Next"
      - Select "Install"
   - UniversalForwarder Setup pop up will flash orange
      - Select "Finish"  

- Install Sysmon
   - In the target-PC, navigate to <a href="https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon">Sysmon Downloads</a>
      - Select "Download Sysmon" 
   - Navigate to <a href="https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml">Sysmon's Configuration File</a> on GitHub
      - This is the sysmon configuration that we will be using
   - Select "Raw"
     
![sysmon git](https://github.com/user-attachments/assets/b9d900d1-ad9a-4a4e-8e64-83191e12ee15)

   - Save in downloads 
   - Extract the Sysmon file
      - Navigate to downloads directory
      - Right click "Sysmon"
      - Select "Extract All"
      - Select "Extract"
   - Copy the file path
     
![copy file path](https://github.com/user-attachments/assets/0c4718e4-afbc-4962-95b7-ac663126f357)
        
   - Open PowerShell as administrator
   - Change directory into the file path 
      - Run: cd (Paste the file path of the extracted directory here)
      - ex. ```cd C:\Users\hacki\Downloads\Sysmon ```
   - Run: ```.\Sysmon64.exe -i ..\sysmonconfig.xml```
      - -i flag indicates that we want to specify a configuration file
      - ../ allows us to go back one directory 
         - sysmon config file is locaed under downloads directory so ../ will allow us to do that
   - Hit "Enter"
   - Select "Agree" to install Sysmon
   - Screen will display "Sysmon64 started"
   - Close PowerShell
 

- Configure Splunk Unviersal Forwarder to specify what data we want to send to our Splunk Server
   - Do this by configuring file called "inputs.conf"
   - Navigate to File Explorer > Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > default > inputs.conf
      - Do NOT want to edit the inputs.conf file under the default directory
   - Create a new file under the local directory and name it inputs.conf
      - Open Notepad as administrator and enter the below information:
         - This instructs Splunk Universal Forwarder to push events related to Application, Security, System, and Sysmon over to Splunk Server
         - Take note of "index=endpoint", whatever falls under these categories will be sent over Splunk and placed under the index "endpoint". If our Splunk Server does not have an index named endpoint, it will not receive any of these events. 


```
[WinEventLog://Application]
index = endpoint
disabled = false

[WinEventLog://Security]
index = endpoint
disabled = false

[WinEventLog://System]
index = endpoint
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = endpoint
disabled = false
renderXml = true
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

```
   - Save file 
      - Save under local directory
         - File Explorer > Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > local > inputs.conf
      - File Name: inputs.conf
      - Save as type; All Files
      - Save
   - Restart
      - Any time you update "inputs.conf" file, you must restart Splunk's Universal Forwarder service
      - Open "Services" as administrator
      - Find "SplunkForwarder"
         - Double click "SplunkForwarder"
         - Select "Log On"
         - Select "Local System Account"
         - Select "Apply"
         - Select "Ok"
      - Right click "SplunkForwarder" and select "Restart" or click "Restart" on the top left
        
![restart splunkfwd](https://github.com/user-attachments/assets/28038e71-6b06-46e3-a8ad-ffa6895d9bca)

   - Select "Start" the service
     
Now, we have our Sysmon & Splunk Universal Forwarder installed along with our updated inputs.conf file.

- Finalize Splunk Server configuration
   - On the target-PC, go to Splunk web portal
      - Search 192.168.10.10:8000 in browser
         - Splunk VM must be running in order for portal to work
      - Log on with credentials created during Splunk install on Splunk Server

![splunk browser](https://github.com/user-attachments/assets/097c7654-8420-493a-81df-401ba3081e36)

   - Once logged on, create "endpoint" index
      - Select "Settings"
      - Select "Indexes"
      - Select "New Index"
      - Index Name: endpoint
      - Select "Save"
        
![endpoint - new index](https://github.com/user-attachments/assets/daa3d6fe-c25e-4489-83f5-2c85b3c654c0)

   - Next, enable Splunk Server to receive data
      - Select "Settings"
      - Select "Forwarding and receiving"
      - Under "Receive data", select "Configure receiving"
      - Select "New Receiving Port"
      - Listen on this port: 9997 
         - If you recall during the set up, 9997 is the default port
      - Select "Save"
   - Should now see data coming in from target-PC 
      - Select "Search & Reporting"
      - Search "index=endpoint"
      - Scroll down to "host" and you will see 1 host, which is the target-PC
         - Once Windows Server is configured, you will see 1 host  

![1 host](https://github.com/user-attachments/assets/d8689fc6-1742-411c-9bf5-63a065210471)


***5. Configure Windows Server***

- Rename the host name of machine
   - In the Start Menu search, search "PC"
   - Select "Properties"
   - Select "Rename this PC"
   - Rename to "ADDC01"
   - Select "Next"
   - Select "Restart Now"
     
- Update static IP to 192.168.10.7
   - Open command prompt
   - Run: ```ipconfig```
      - this will display current IPv4
   - Navigate to network icon at the bottom right of the window
   - Select "Open Network & Internet Settings"
   - Select "Change adapter options"
   - Right click the adapter, Ethernet
   - Select "Properties"
   - Select "Internet Protocol Version 4 (TCP/IPv4)" > "Properties"
   - Select "Use the following IP address"
   - Configure as shown below:
     
![addc ip](https://github.com/user-attachments/assets/c7dc690f-405f-4408-912b-d1d686efe638)

   - Run: ```ipconfig to view updated IPv4```

![addc static](https://github.com/user-attachments/assets/29b3e82b-bcdb-4826-b59c-b8793a461d57)

- Install Splunk Universal Forwarder on target-PC (Please note: installing Splunk Universal Forwarder and Sysmon on the ADDC VM will follow these exact same steps)
   - Run Splunk VM (the following steps will not work if VM is not running)
   - In the target-PC, open the browser and type 192.168.10.10:8000
      - Splunk listens on port 8000 
   - In the target-PC, visit <a href="https://www.splunk.com/">Splunk</a>
      - Log on to account
      - Select "Products"
      - Select "Free Trials & Downloads"
      -  Scroll down to Universal Forwarder & select "Get My Free Download"
      -  Select the compatible OS
   - Once download is completed, double click the msi file
      - Select "Check this box to accept the License Agreemnent"
      - Select "An on-premises Splunk Enterprise Instance"
      - Select "Next"
      - Choose your credentials
      - For "Receiving Indexer", this is going to be our Splunk Server's IP address
         - Hostname or IP: 192.168.10.7
         - Default port for Splunk when receiving events is: 9997
      - Select "Next"
      - Select "Install"
   - UniversalForwarder Setup pop up will flash orange
      - Select "Finish"  

- Install Sysmon
   - In the ADDC01 machine, navigate to <a href="https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon">Sysmon Downloads</a>
      - Select "Download Sysmon" 
   - Navigate to <a href="https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml">Sysmon's Configuration File</a> on GitHub
      - This is the sysmon configuration that we will be using
   - Select "Raw"
     
![sysmon git](https://github.com/user-attachments/assets/b9d900d1-ad9a-4a4e-8e64-83191e12ee15)

   - Save in downloads 
   - Extract the Sysmon file
      - Navigate to downloads directory
      - Right click "Sysmon"
      - Select "Extract All"
      - Select "Extract"
   - Copy the file path
     
![copy file path](https://github.com/user-attachments/assets/0c4718e4-afbc-4962-95b7-ac663126f357)
        
   - Open PowerShell as administrator
   - Change directory into the file path 
      - Run: cd (Paste the file path of the extracted directory here)
      - ex. cd C:\Users\hacki\Downloads\Sysmon 
   - Run: ``` .\Sysmon64.exe -i ..\sysmonconfig.xml ```
      - -i flag indicates that we want to specify a configuration file
      - ../ allows us to go back one directory 
         - sysmon config file is located under downloads directory so ../ will allow us to do that
   - Hit "Enter"
   - Select "Agree" to install Sysmon
   - Screen will display "Sysmon64 started"
   - Close PowerShell
 

- Configure Splunk Unviersal Forwarder to specify what data we want to send to our Splunk Server
   - Do this by configuring file called "inputs.conf"
   - Navigate to File Explorer > Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > default > inputs.conf
      - Do NOT want to edit the inputs.conf file under the default directory
   - Create a new file under the local directory and name it inputs.conf
      - Open Notepad as administrator and enter the below information:
         - This instructs Splunk Universal Forwarder to push events related to Application, Security, System, and Sysmon over to Splunk Server
         - Take note of "index=endpoint", whatever falls under these categories will be sent over Splunk and placed under the index "endpoint". If our Splunk Server does not have an index named endpoint, it will not receive any of these events. 


```
[WinEventLog://Application]
index = endpoint
disabled = false

[WinEventLog://Security]
index = endpoint
disabled = false

[WinEventLog://System]
index = endpoint
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = endpoint
disabled = false
renderXml = true
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

```
   - Save file 
      - Save under local directory
         - File Explorer > Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > local > inputs.conf
      - File Name: inputs.conf
      - Save as type; All Files
      - Save
   - Restart
      - Any time you update "inputs.conf" file, you must restart Splunk's Universal Forwarder service
      - Open "Services" as administrator
      - Find "SplunkForwarder"
         - Double click "SplunkForwarder"
         - Select "Log On"
         - Select "Local System Account"
         - Select "Apply"
         - Select "Ok"
      - Right click "SplunkForwarder" and select "Restart" or click "Restart" on the top left

![restart splunkfwd](https://github.com/user-attachments/assets/59ca3d70-f3e7-4d99-9734-8054a10d038c)

   - Select "Start" the service
     
Now, we have our Sysmon & Splunk Universal Forwarder installed along with our updated inputs.conf file.

   - Now, we should see data coming in from ADDC01 as well as the target-PC
      - Log onto Splunk web portal by searching 192.168.10.10:8000 in browser
         - Splunk VM must be running in order for portal to work
      - Log on with credentials created during Splunk install on Splunk Server
      - Select "Search & Reporting"
      - Search "index=endpoint"
      - Scroll down to "host" and you will see 2 hosts
        
![2 hostss](https://github.com/user-attachments/assets/8841c579-4b50-4e38-9e3f-3e1ab3959560)

***Summary***

We have successfully installed Sysmon and Splunk on both our Windows machine (target-PC) and Windows Server (ADDC01). Now, when logged into the Splunk Server on either machine, you should be able to view the telemetry from both machines. 

![Splunk hosts](https://github.com/user-attachments/assets/e51494bd-bfea-47c8-9f24-3bbf92377e75)


## Phase 3: Active Directory and Control Domain

***1. Setup Active Directory Domain Services on Windows Server***

- Install ADDS aka Active Directory Domain Services
   - On Windows Server, open "Server Manager"
   - Select "Manage" > Add Roles & Features > Next > Role-based of feature-based installation > Next > select ADDC01 > Next > select "Active Directory Domain      Services > Add features > Next > Next > Install
   - Once installed, select "close"
     
- Promote the server & create a new domain
   - Open Server Manager 
   - Select the flag icon
   - Select “Promote the server to a domain controller"
   - Select “Add a new forest” since we are creating a new domain
      - Root domain name: yourdomainname.local
         - Domain name must have top level domain; cannot just be "yourdomainname"
            - ex. hackingprincesa.local, hackingprincesa.test, yourdomainname.test, etc 
         - Attackers love to attack domain controllers since it has access to everything. Access to this file grants access to everything related to AD including passwords hashes.
         - Create a password
         - Install
         - Machine will automatically restart
- Log in
   - On logon screen, will see domainame\Administrator
      - This indicates that we have successfully installed ADDS and promoted our server to a domain controller.
                     
- Create users
   - Open Server Manager
   - Select "Tools"
   - Select "Active Directory Users and Computers"
      - Here we can create objects such as users, computers, groups, etc.
   - Add a new OU aka Organizational Unit
      - Please note: OUs can be used to group objects by function, location, department, or other criteria. In a work environment, OUs are usually used as            departments.
      - Right click the domain > select "New" > Organizational Unit
      - Name: "IT"
        
![OU](https://github.com/user-attachments/assets/c19d9665-75c0-4905-9b47-db82e97e424e)

   - Within this new OU, create a new user
      - Select "New" > "User"
![ner user](https://github.com/user-attachments/assets/603d8586-e089-4e62-8e3c-38bd34031275)

      - Fill out the form with new user information to successfully add a new user.
      
![new user in IT](https://github.com/user-attachments/assets/cdd721ba-ef01-4208-8829-3a32f06268fb)

Active Directory is now set up and our server is now promoted to domain controller.

              
***2. Configure Windows 10 Machine to Join New Domain***

- Log onto Windows 10 machine and join our newly created domain & authenticate using Jenny Smith account
   - Log onto Windows 10 machine (target-PC) 
   - Search PC > select "Properties" > Advanced system settings > Computer Name > Change
   - Under "Member of", select "Domain"
     
![member of domain](https://github.com/user-attachments/assets/472f64e4-b870-4b27-8308-0852db7e995c)

   - You will get an error message; this is related to the DNS we configured earlier. Originally, we had our Preferred DNS server set to 8.8.8.8, which is Google's DNS server. We want to update the Preferred DNS server so that it is pointing to our domain controller, which is 192.168.10.7.
      - Update the Preferred DNS Server to the domain controller's IP address:
         - Navigate to network icon at the bottom right of the window
         - Select "Open Network & Internet Settings"
         - Select "Change adapter options"
         - Right click the adapter, Ethernet
         - Select "Properties"
         - Select "Internet Protocol Version 4 (TCP/IPv4)" > "Properties"
         - Select "Use the following IP address"
         - Configure as shown below:

![windows static IP](https://github.com/user-attachments/assets/f27e01d7-6811-4b93-8f19-13cf91856222)

   - Confirm updated DNS Server in Command Prompt
      - Open Command Prompt
      - Run: ```ipconfig /all```

![cmnd prompt - dns conf](https://github.com/user-attachments/assets/28405fc6-bafb-4007-b7d2-cc7bea5d43e1)

   - Now, we can join the domain
      - Search PC > select "Properties" > Advanced system settings > Computer Name > Change
      - Under "Member of", select "Domain"
      - Select "Ok"
      - Will be prompted to enter credentials
         - User the administrator account of the server to log on as it has the appropiate permissions
            - The administrator account will grant access to join domain 
           
![admin cred](https://github.com/user-attachments/assets/20293e09-eb06-49a5-9b69-d70349d26861)

   - You will get a pop up welcoming you to the domain.
   - Computer will prompt you to restart
     
- Once you are back on the log on screen, confirm that new user has successfully joined the domain
   - Select "Other User"
   - Log on with your newly created user Jenny Smith or Maria Rodriguez
   - Type "jsmith" and the password

![jsmith log on](https://github.com/user-attachments/assets/bdba0549-81dd-4708-ae98-06bf6fe3b292)


***Summary***

We have successfully configured our Active Directory server, created new users, joined our computer to a new domain, and logged in as a domain user.

![welcome jenny](https://github.com/user-attachments/assets/d731160b-07b3-43b6-ad1c-751e7a178469)


## Phase 4: Brute Force Attack

***1. Configure Network***

- Log onto Kali Linux machine with default credentials
- Configure a static IP address to 192.168.10.250
   - Right click the Ethernet icon
   - Select "Edit Connections"
     
![edit connection](https://github.com/user-attachments/assets/2570ea72-9b77-4d14-9b90-399d4dc70ef9)

   - Select Wired connection 1
   - Select the cog icon
   - Select IPv4 Seetings
   - Configure and save:

![kali static ip](https://github.com/user-attachments/assets/8dff6f03-48ba-41cc-b28a-879cf7d3760d)

   - To reflect changes, select the Ethernet icon and select "Disconnect
   - Select the Ethernet icon again and select "Wired connection 1"
   - Confirm that the static IP has been updated
      - Open terminal
      - Run: ```ip a```
        
![kali terminal](https://github.com/user-attachments/assets/346e6ff5-1e7b-494c-a017-2eb9589f2753)

   - Verify connectivity
      - Run: ```ping google.com```
      - Run: ```ping 192.168.10.10```
        
- Update & upgrade repositories
   - Run: ```sudo apt-get update && sudo apt-get upgrade -y```
 
***2. Set up the Attack***
     
- Set up attack, once update & upgrade is complete
   - First, create a new directory called AD Project
      - Run: ```mkdir AD-Project```
      - This will create a directory on our desktop. All the files that we will create & use, will be put into this directory.

- Install crowbar
   - Run: ```sudo apt-get install -y crowbar```
   - Crowbar is the tool that we will be using to perform brute force attacks. We can target either our ADDC machine or our target machine.
   - Please note: We are using this tool for educational purposes only; please only attack consenting targets.

- Use rockyou file
	- Rockyou is a popular world list that comes with Kali Linux
- Change to the directory where you will find rockyou file  
	- Run: ```cd /usr/share/wordlists/```
	- Run: ```ls```
- Unzip rockyou.txt.gz file using gunzip
	- Run: ```sudo gunzip rockyou.txt.gz```
 	- Run: ```ls```
 Now, you should see rockyou.txt:

![find rockyou](https://github.com/user-attachments/assets/269ba022-6bc5-44ce-93ab-0fccf00d2887)

- Copy this file onto AD Project folder
	- Run: ```cp rockyou.txt ~/Desktop/AD-Project/```
- Change into AD Project directory
	- cd ~/Desktop/AD-Project/
- Verify that it rockyou.txt copied over into this directory
	- Run: ```clear ```
 	- Run: ```ls -lh```

Here, you can see that this file contains 134 M meaning it contains quite a bit of passwords. For the sake of the project, we will only use the first 20 lines. 

![rockyou](https://github.com/user-attachments/assets/17bf1e10-4e21-41b0-85b3-b73b3584de63)

- To only use the first 20 lines:
	- Run: ```head -n 20 rockyou.txt```
   
Here, you will see 20 lines.

![rockyou 20](https://github.com/user-attachments/assets/7380ef1a-0730-432a-92bd-1276d5fd979b)

- Clear it out
	- Run: ```clear ```
- Output it to a file called passwords.txt
	- Run: ```head -n 20 rockyou.txt > passwords.txt```
 	- Run: ```ls```
  		- Now, you will see the rockyou.txt & the passwords.txt file
      
![kali - pw   ru file](https://github.com/user-attachments/assets/3f70cdc7-d74c-4454-bbff-971d29bf38e0)

- Open passwords.txt
	- Run: ```cat passwords.txt```
 		-  Should see the 20 lines
     
![kali pw](https://github.com/user-attachments/assets/70141978-c3c0-4255-84dd-dfc3875c48bc)

- Let’s say we want to target a certain PW
	- Run: ```nano passwords.txt```
 		- This command allows you to edit the file
   		- Add PW at end
     		- Save by holding control + x
       		- Type "Y"
         	- Enter
            
![kali PW file edit](https://github.com/user-attachments/assets/5ae3e437-f6bc-4be6-b664-63066a1caf54)

- Verify that edit was saved
	- Run: ```cat passwords.txt```
 	- Should see 21 lines
    
![cd pw file](https://github.com/user-attachments/assets/5df58b44-f3cc-4a49-ac96-65d25566a80f)

***3. Enable RDP***

- Before we launch the attack, we want to enable Remote Desktop Protocol aka RDP
- Open Windows target-PC
- Enable remote desktop on target-PC
   - Go to the PC’s properties
   - Search PC
- Select Properties  
- Select Advanced system settings
  - Will prompt you to logon with administrator credentials
  - Select Remote tab
  - Select Allow remote connections to this computer
  	  
![allow RDP](https://github.com/user-attachments/assets/4c97d85e-6d9e-4684-845c-4f0d8b70bca7)

  - Select Select Users…
  - Select Add
  - Add your users
	- Type "jsmith" for Jenny Smith, then select "Check Names"
  	- Type "tsmith" for Terry Smith, then select "Check Names"
  - Select OK
	- Here you will see both users added
  	     
![RDP](https://github.com/user-attachments/assets/616ed192-c66c-43d0-a56b-334c2165088d)

  - Select OK
  - Select Apply
  - Select OK
   
- We have successfully enabled remote desktop onto target-PC.
   - You can also enable RDP on your ADDC machine if you'd like.

***4. Attack***
  
- Open Kali Linux machine
- Open terminal
- Open crowbar's, the brute force tool, help menu
	- Run: crowbar -h
 	- This command allows us to see what options are available. The service that we are interested in is RDP; hence, why we are using this tool & why we 	enabled RDP.

![crowbar -hh](https://github.com/user-attachments/assets/584e9efa-afc1-404c-8cc2-fca84056b0c3)

- Attack
	- Run: ```crowbar -b rdp -u tmsith -C passwords.txt -s 192.168.10.100/32```
 		- b specifies service (rdp)
  		- u specifies account of interest (tsmith)
   		- C specify a PW list (passwords.txt)
   		- s to specify source IP (target machine’s IP)
     			- must specify a cider notation 
   			- use /32 instead of /24 because I want to only target this one IP
        
Once we execute crowbar, it will start trying out all of the password's that are listed under the password.txt file 
        
![crowbar](https://github.com/user-attachments/assets/e479b094-41ab-4aa2-9ce4-08dc054ca656)

View telemetry generared on Splunk

- Open Splunk in the Windows 10 (target-PC) machine
	- Search "192.168.10.10:8000" in browser
- Select Search & Reporting
- Narrow down search
	- Search "index=endpoint tsmith" 
- Select EventCode under "Interesting Fields"
	- Here, you will see a total count of 78 for event code 4625 meaning that there were 78 failed attempts to logon
 		- Google event id’s you don’t know
   		- ex. 4625: An account failed to log 

![4625](https://github.com/user-attachments/assets/d05735cd-79c6-49bd-b766-23759f3f56c9)

- Select 4625
	- This will automatically update our query
- Look at the time of all events
	- Will see that they all happened around the same time, which is a clear indicator of a brute force attack

![splunk logtimes](https://github.com/user-attachments/assets/9912168d-958e-45c1-b04b-38fa7d026696)

- Search "eventcode=4624" and you will see 1 event
	- 4624: An account was successfully logged on
 	- A successful logon after many failed attempts is another another Indicator of Compromise (IOC).
- Expand this event to view attack details
	- Select "Show all 70 lines" to investigate further
 	- You will see a workstation named "kali" as well as the IP address that is trying to login; this is who performed the brute force attack
  		- If you refer to the diagram that we created in the very first step of this project, you will see that the IP address of our Kali Linux Attacker Machine, 192.168.10.250, matches the IP address in this event 
    
![attacker IP](https://github.com/user-attachments/assets/d38db8c3-048f-4562-8417-cbbec1bfedd9)


***5. Use ART to Run Tests on Target Machine***

Atomic Red Team is an open source library of tests designed to test your organization's security controls.

- Start the Windows 10 (target-PC) machine
- Open Powershell as an administrator
	- Run: ``` Set-ExecutionPolicy Bypass CurrentUser ```
	- Type "Y"
- Next, set an exclusion for the entire c drive as Microsoft Defender will detect & remove some of the files from ART
	- Select on up arrow on Windows
 	- Select Windows Security icon
  	- Select Virus & threat protection
  	- Select Manage settings
  	- Select “Add or remove exclusions”
  	- Select Add an exclusion
  	- Select folder
  	- Select This PC
  	- Select C drive
  	- Select “Select Folder”
  		- Will need to logon as administrator
  	- Will see the exclusion for the c drive

![add an exlusion on Microsoft Defender](https://github.com/user-attachments/assets/acf45bdd-6642-4751-81d1-451514788631)

- On the target-PC, visit <a href="https://github.com/redcanaryco/atomic-red-team">Atomic Red Team GitHub</a>
	- Select Code
 	- Select Download ZIP

![code ART](https://github.com/user-attachments/assets/49820354-592e-4451-bc60-7ea34bb8e61a)

- Scroll down on <a href="https://github.com/redcanaryco/atomic-red-team">Atomic Red Team GitHub</a>
	- Select Invoke-Atomic
		- Select Code
 		- Select Download ZIP
     
- Create a new folder in C drive
	- Name the folder "AtomicRedTeam"
 - Move zip files that we downloaded to the "AtomicRedTeam" folder
   
 - Extract files
 	- If you didn't turn off "Virus & threat protection" earlier, the attacks in these folders would set off a plethora of alarms on advising that a 	threat has been detected  
 - Once extracted, delete the zips
 - Clean up files
 	- There’s a duplicate directory, make sure to move appropriate files to one directory and delete the duplicate directory
    
  - Delete “master” out of file name “invoke-atomicredteam-master”
  	- invoke-atomicredteam
     
- Select atomic-red-team-master
- Move "atomics" file to AtomicRedTeam folder that we created
	- Now, there should be 3 folders in the AtomicRedTeam folder 

![ART folder ](https://github.com/user-attachments/assets/fa5b5cea-fed0-484d-a1e3-7f2e4ecbdb9c)

Next, we are going to setup powershell by following the installaton instructions on <a href="https://github.com/redcanaryco/atomic-red-team">Atomic Red Team GitHub</a>

- Visit <a href="https://github.com/redcanaryco/atomic-red-team">Atomic Red Team GitHub</a>
- Select Installation and Usage instructions
	- Here you will find the scripts that we will be copying into PowerShell 
- Select Installation 
- Powershell can’t run scripts so we will combat by first running:
	- Run: ``` powershell -exec bypass or set-ExecutionPolicy Bypass CurrentUser ```
  	- Type "Y"
  	- With this command, we can now run scripts
- Install module
	- Run: ``` Install-Module -Name invoke-atomicredteam,powershell-yaml -Scope CurrentUser ```
- Import module
	- Run: ``` Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force ```
 	- Please take note of the path of the above command. This is why we cleaned up the files a few moments ago.

![powershell -exec bypass  install invoke ART](https://github.com/user-attachments/assets/4d5457aa-3880-4e0f-ab99-6847f1acedb8)

- Verify that module was imported correctly
	- Run: ``` get-module ```
 	- Should see "Invoke-AtomicRedTeam" script
    
- Now, you can use one of the tactics from the atomics folder
	- Choose which tactic you want to use
 	- Run: ``` Invoke-AtomicTest T1136.001 ```
  		- This attack creates a new windows admin user called “newlocaluser”

![ART attack on Powershell](https://github.com/user-attachments/assets/fb653f49-5121-4c64-b82f-46ffa252c123)

Image below is the output PowerShell produces when the command has been completed successfully.

![T1136 attack successful](https://github.com/user-attachments/assets/ac54db04-3326-47e4-a39a-8e8ffe3d441d)

- Search in Splunk for telemtry this event has created 
	- index=endpoint powershell or index=endpoint newlocaluser
 	- If no events, that will tell you that you’re blind to this activity. If an attacker compromised a system and created a local account with your current settings, you will not be able to detect activity. Hence why ATR is good for SOC as it identifies the gaps and visibility for you and will also generate the telemetry to see if you can actually detect that activity

In our scenario, it looks like we were able to detect the activity with "index=endpoint powershell", but not with "index=endpoint newlocaluser". Tests like these show how ART is incredibly valuable for an organization.

![splunk t1136](https://github.com/user-attachments/assets/962c5847-690e-4d14-9379-1ab1ca40082d)

- Perform another attack
	- Open PowerShell 
	- Run: Invoke-AtomicTest T1059.001
 	- This attack is called Command and Scripting Interpreter using Powershell
- Search in Splunk
	- Type "index=endpoint command and scripting" in search bar 
 
![T1059](https://github.com/user-attachments/assets/7ec76d1c-168e-4042-8b00-05c7b150d6e2)

## Conclusion 

Congratulations, you've reached the end of the guide! Your setup should be fully operational, with successful communication between four Virtual Machines: the Windows 10 Target Machine, Kali Linux Attacker Machine, Splunk Server, and Windows Server (ADDC). You should now be able to view events in Splunk, test its functionality, and assess defenses using tools like the Crowbar brute-force password cracker, the ART framework, and more.

A huge thanks to MyDFIR on YouTube for the tutorial. I learned so much throughout this process and was able to turn my theoretical knowledge into hands-on experience.  

## References & Resources:
- <a href="https://www.youtube.com/watch?v=mWqYyl89QaY&t=541s">Active Directory Project by MyDFIR</a>
- <a href="https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/active-directory-overview">Microsoft Active Directory Documentation</a>
- <a href="https://docs.splunk.com/Documentation">Splunk Documentation</a>
- <a href="https://github.com/redcanaryco/atomic-red-team">Atomic Red Team GitHub</a>
- <a href="https://www.youtube.com/watch?v=_xW3fAumh1c&t=575s">Atomic Red Team YouTube Guide to Setup</a>
- <a href="https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon">Sysmon Documentation</a>
- <a href="https://docs.splunk.com/Documentation/Forwarder">Splunk Universal Forwarder Documentation</a>
