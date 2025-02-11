<h1>Active Directory Home Lab using PowerShell</h1>


<h2>Description</h2>
This project involves setting up a home lab envrionment to stimulate an enterprise Active Directory (AD) setup using Windows Server 2019 as the Domain Controller (DC) and Windows 10 as a client machine. This lab will be hosted on Oracle VirtualBox, with configurations and automation done through PowerShell script. Configuring and running this lab will allow you to test and practice AD management, Group Policy and user authentication in a controlled environment. You will also gain an understanding of how windows domain networks work. 
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b>
- <b>Command Prompt (CMD)</b>
- <b>Oracle VirtualBox<b>
- <b>Windows Server 2019 ISO (Domain Controller)<b>
- <b>Windows 10 ISO (Client)<b>
- <b>Active Directory Domain Services (AD DS)<b>
- <b>Domain Name System (DNS)<b>
- <b>Dynamic Host Configuration Protocol (DHCP)<b>
- <b>Remote Access Service / Network Address Translation (RAS/NAT)<b>

<h2>Environments Used </h2>

- <b>Host Machine: Windows (with Oracle VirtualBox Installed)</b>
- <b>Windows Server 2019 VM - configured as Domain Controller<b>
- <b>Windows 10 VM - configured as a domain-joined Client<b>
- <b>Network Configurations - 2 adapters:</b>
  1. NAT
  2. Internal Network<b>


<h2>Project walk-through:</h2>

Step 1: Installing & Configuring Windows Server 2019 on Oracle VirtualBox
1. Install Windows Server 2019 on the first VM.
2. Under network configuration: Assign Adapter 1 as NAT and Adapter 2 as Internal Nework, static IP address to the server.
3. Rename the machine (e.g., MYDOMAIN) and restart.
4. Install Active Directory Domain Services (AD DS):
   - Open Server Manager → Add Roles and Features
   - Select Active Directory Domain Services (AD DS) and DNS Server
   - Complete the installation and restart the VM.
5. Promote the Server to a Domain Controller:
   - Open Server Manager → Click Promote this server to a domain controller
   - Select "Add a new forest" and enter a domain name (e.g., homelab.local).
   - Configure DNS and complete the promotion.


<p align="center">
Creating Users with Powershell: <br/>
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>

