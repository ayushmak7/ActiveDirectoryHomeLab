<h1>Active Directory Home Lab using PowerShell</h1>


<h2>Description</h2>
This project involves setting up a home lab envrionment to stimulate an enterprise Active Directory (AD) setup using Windows Server 2019 as the Domain Controller (DC) and Windows 10 as a client machine. This lab will be hosted on Oracle VirtualBox, with configurations and automation done through PowerShell script. Configuring and running this lab will allow you to test and practice AD management, Group Policy and user authentication in a controlled environment. You will also gain an understanding of how windows domain networks work. 
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b>
- <b>Command Prompt (CMD)</b>
- <b>Oracle VirtualBox</b>
- <b>Windows Server 2019 ISO (Domain Controller)</b>
- <b>Windows 10 ISO (Client)</b>
- <b>Active Directory Domain Services (AD DS)</b>
- <b>Domain Name System (DNS)</b>
- <b>Dynamic Host Configuration Protocol (DHCP)</b>
- <b>Remote Access Service / Network Address Translation (RAS/NAT)</b>


<h2>Environments Used </h2>

- <b>Host Machine: Windows (with Oracle VirtualBox Installed)</b>
- <b>Windows Server 2019 VM - configured as Domain Controller</b>
- <b>Windows 10 VM - configured as a domain-joined Client</b>
- <b>Network Configurations - 2 network adapters:</b>
  1. Adapter 1 (NAT) → Provides internet access to the Domain Controller.
  2. Adapter 2 (Internal Network - intnet) → Allows communication between VMs.


<h2>Project walk-through:</h2>

**Step 1: Installing & Configuring Windows Server 2019 (DC) on Oracle VirtualBox**
1. Install Windows Server 2019 on the first VM.
2. VirtualBox network settings: Configure Windows Server 2019 VM:
   - Open VirtualBox → Select Windows Server 2019 VM → Settings → Network.
   - Adapter 1 (Attached to : NAT - Internet Access)
   - Adapter 2 (Attached to : Internal Network - intnet)
      
4. Rename the device (e.g., DC-winserver19) and restart.
5. Install **Active Directory Domain Services (AD DS)** on Windows Server 2019 (DC):
   - Open Server Manager → Add Roles and Features
   - Select Active Directory Domain Services (AD DS) and DNS Server
   - Complete the installation and restart the VM.
6. Promote the Server to a Domain Controller:
   - Open Server Manager → Click Promote this server to a domain controller
   - Select "Add a new forest" and enter a domain name (e.g., mydomain.com as FQDN).
   - Configure DNS and complete the promotion.
7. Once the Domain Controller is set up, create a domain admin account:
     - Right-click create an Organizational Unit (OU) and name it ADMINS.
     - Right-click ADMINS -> New -> User and name.
 - ✅ Active Directory (AD DS) on Windows Server 2019

8. Set Up **Routing and Remote Access (RAS) for NAT** on Windows Server 2019 (DC)-> This step allows the Windows 10 client to access the internet via the domain controller.
    1.  Open Server Manager on Windows Server 2019.
    2.  Click Manage → Add Roles and Features.
    3.  Role-based or feature-based installation → Click Next.
    4.  Select your server → Click Next.
    5.  Under Server Roles, select:
       - Remote Access
    6. Click Next, then Next again for features.
    7. Under Role Services, select:
       - Routing
       - Click Add Features when prompted.
     8. Click Next → Install and wait for installation to complete.
     9. After installation, open Server Manager → Tools → Routing and Remote Access.
     10. Right-click your server name → Configure and Enable Routing and Remote Access.
     11. Choose “Network Address Translation (NAT)” → Click Next.
     12. Select NAT on Adapter 1 (NAT Interface - **Internet**) → Click Next.
     13. Click Finish and Start the service when prompted.
- ✅Now, the Domain Controller acts as a NAT router for the internal network. Routing and Remote Access (RAS) NAT -> Enables Windows 10 clients to access the internet via the DC.
  
8. Set up **DHCP** on Windows Server19 (DC):
     1. Install DHCP using Server Manager: Open Server Manager → Manage → Add Roles and Features.
     2. Click Next → Select Role-based or Feature-based Installation.
     3. Select your server → Click Next.
     4. Under Server Roles, select:
        - DHCP Server
     5. Click Next → Next → Install and wait for installation to complete.
     6. Click Close when done.
   
9. Configure **DHCP Server Scope**:
    1. Open Server Manager → Tools → DHCP.
    2. In the left panel, expand your server name → IPv4.
    3. Right-click IPv4 → New Scope.
    4. Click Next, then enter:
       - Scope Name: 172.16.0.100-200 (Internal network IP range)
       - Starting IP: 172.16.0.100
       -  Ending IP: 172.16.0.200
       -  Lenght: 24
       -  Subnet Mask: 255.255.255.0
       - Click Next.
    5. Add Exclusions (Optional) -> If you want to exclude certain addresses (e.g., 192.168.1.1 for the DC), add them here. Click Next.
    6. Lease Duration:
        - Keep default (8 days) or adjust as needed → Click Next.
    7. Configure DHCP Options:
       - Router (Gateway): 192.168.1.1 → Click Next.
       -  DNS Servers:
            - Preferred DNS: 192.168.1.1
            - Click Next.
    8. Click Activate Scope → Finish.
- ✅ Now, DHCP server will automatically assign IPs to Windows 10 clients!

**Step 2: Creating Active Directory Users with PowerShell on Windows Server (DC)**
1. Now that the Active Directory (AD) Domain Controller is set up, you can automate user creation using a PowerShell script. The script reads names from a names.txt file and creates users in AD.
  - Before running the script, confirm:
    - ✅ Active Directory Domain Services (AD DS) is installed on Windows Server 2019.
    - ✅ Windows Server is a Domain Controller (DC).
    - ✅ PowerShell Execution Policy allows scripts (modify if needed using Set-ExecutionPolicy Unrestricted).
    - ✅Save the PowerShell script as Create-ADUsers.ps1 and store it and names.txt file in the same directory (e.g., C:\Users\Administrator\Desktop\ADUsers).
      
      
2. Prepare names.txt File
- This file should contain one user per line, for example:
  - John Doe
  - Jane Smith
  - Michael Jackson
  - Anthony Davis
3.  Create PowerShell Script (Create-ADUsers.ps1)
- Save the following PowerShell script as Create-ADUsers.ps1 in the same folder as names.txt file (e.g., C:\Users\Administrator\Desktop\ADUsers).
- PowerShell Script:

  
```powershell
# This is a PowerShell script to create Active Directory users

# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
```

  - Navigate to the directory where the script is stored and run the following commands and press "Run Script" button

<p align="center">
Creating Users with Powershell: <br/>
<img src="https://i.imgur.com/o3grx4D.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />  
<br />
Confirm new users are created:  <br/>
<img src="https://i.imgur.com/RZiRQg2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />




**Step 3: Installing & Configuring Windows 10 (Client) on Oracle VirtualBox**
1. Install Windows 10 on the second VM.
2. VirtualBox network settings: Configure Windows 10 (Client) VM:  
   - Open VirtualBox → Select Windows 10 VM → Settings → Network.
   - Adapter 1 (Attached to : Internal Network - intnet -> Internet via NAT on DC)
3. Join Windows 10 to the Domain:
    - Go to Settings → System → About → Rename this PC (Advanced settings)
    - Click Change → Enter Computer name as "CLIENT1" 
    - Select Domain and enter "mydomain.com" and provide domain admin credentials (Administrator account)
    -  Restart the VM.
- ✅ Windows 10 client connected to the domain which ensures that the Windows 10 clients can communicate with AD and access the internet through NAT.

 Join Windows 10 to the Domain:<br/>
<img src="https://i.imgur.com/l4G3oqf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />







<h2>Testing DHCP and Internet Access</h2>
Check Assigned IP on Windows 10:  <br/>
<img src="https://i.imgur.com/P0grTQm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
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

