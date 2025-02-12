<h1>💻Active Directory Home Lab using PowerShell</h1>


<h2>📑Description</h2>
This lab sets up an Active Directory (AD) home lab on Oracle VirtualBox to simulate a real-world enterprise environment using Windows Server 2019 as a Domain Controller (DC) and Windows 10 as a domain-joined client. The setup includes configuring Active Directory Domain Services (AD DS), DHCP, Remote Access Service (RAS) with NAT for internet access, and automting bulk user creation using PowerShell scripting. The goal is to gain hands-on experience in domain management, network configuration, and user administration within an enterprise setup. 🚀
<br>


<h2>🛠️Languages and Utilities Used</h2>

- <b>PowerShell</b>
- <b>Command Prompt (CMD)</b>
- <b>Oracle VirtualBox</b>
- <b>Windows Server 2019 ISO (Domain Controller)</b>
- <b>Windows 10 ISO (Client)</b>
- <b>Active Directory Domain Services (AD DS)</b>
- <b>Domain Name System (DNS)</b>
- <b>Dynamic Host Configuration Protocol (DHCP)</b>
- <b>Remote Access Service / Network Address Translation (RAS/NAT)</b>


<h2>🖥️Environments Used </h2>

- <b>Host Machine: Windows (with Oracle VirtualBox Installed)</b>
- <b>Windows Server 2019 VM - configured as Domain Controller</b>
- <b>Windows 10 VM - configured as a domain-joined Client</b>
- <b>Network Configurations - 2 network adapters:</b>
  1. Adapter 1 (NAT) → Provides internet access to the Domain Controller.
  2. Adapter 2 (Internal Network - intnet) → Allows communication between VMs.

<h2>🎯 Objective</h2>

1. Set up and configure **Active Directory Domain Services (AD DS)** on **Windows Server 2019**.
2. Promote the server to a **Domain Controller (DC)** and create a domain (mydomain.com).
3. Configure **DHCP** on the Domain Controller to assign IP addresses dynamically.
4. Set up **RAS/NAT** on the DC to allow internal network VMs to access the internet.
5. Join a **Windows 10 client** machine to the domain **(mydomain.com)**.
6. Create Active Directory users using a **PowerShell script**, automating bulk (+1K) user creation.
7. **Verify** that users can log in to the domain from the Windows 10 client.



<h2>🛠️Project walk-through:</h2>

**Step 1: Installing & Configuring Windows Server 2019 (DC) on Oracle VirtualBox**
1. Install Windows Server 2019 on the first VM.
2. Configure two network adapters on Windows Server 2019 VM:
   - Open VirtualBox → Select Windows Server 2019 VM → Settings → Network.
   - Adapter 1: NAT (for internet access)
   - Adapter 2: Internal Network (for domain communication)
      - Assign a static IP to the internal network adapter (e.g.,172.16.0.1).
3. Rename the device (e.g., DC-winserver19) and restart.
4. Install **Active Directory Domain Services (AD DS)** on Windows Server 2019 (DC):
   - Open Server Manager → Add Roles and Features
   - Select Active Directory Domain Services (AD DS) and DNS Server
   - Complete the installation and restart the VM.
5. Promote the Server to a Domain Controller:
   - Open Server Manager → Click Promote this server to a domain controller
   - Select "Add a new forest" and enter a domain name (e.g., mydomain.com as FQDN).
   - Configure DNS and complete the promotion.
6. Once the Domain Controller is set up, create a domain admin account:
     - Right-click create an Organizational Unit (OU) and name it ADMINS.
     - Right-click ADMINS -> New -> User and name.
 - ✅ Active Directory (AD DS) on Windows Server 2019

7. Set Up **Routing and Remote Access (RAS) for NAT** on Windows Server 2019 (DC)-> This step allows the Windows 10 client to access the internet via the domain controller.
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
   - Adapter 1 (Attached to : Internal Network - name: intnet -> Internet via NAT on DC)
3. Join Windows 10 to the Domain:
    - Go to Settings → System → About → Rename this PC (Advanced settings)
    - Click Change → Enter Computer name as "CLIENT1" 
    - Select Domain and enter "mydomain.com" and provide domain admin credentials (Administrator account)
    -  Restart the VM.
- ✅ Windows 10 client connected to the domain which ensures that the Windows 10 clients can communicate with AD and access the internet through NAT.

<b>Join Windows 10 to the Domain:</b><br/>
<img src="https://i.imgur.com/l4G3oqf.png" height="80%" width="80%" alt="Windows 10 Steps"/>
<br>


<b>Test Domain Authentication:</b><br>
Log into the Windows 10 machine using one of the newly created user accounts. Verify that domain authentication is working.<br/>
<img src="https://i.imgur.com/in7viYg.png" height="80%" width="80%" alt="Windows 10 Steps"/>
<br>

<h2>Testing DHCP and Internet Access</h2>
<b>Check Assigned IP on Windows 10 Client</b> (Expected output: IP should be in the 172.16.0.100-200 range, assigned by DHCP):<br/>
<img src="https://i.imgur.com/MQdNbrM.png" height="80%" width="80%" alt="Windows 10 Steps"/>
<br>


<b>Test Domain Controller Connection</b>(Expected output: Replies from 172.16.0.1, confirming connectivity):<br/>
<img src="https://i.imgur.com/gZpyYiv.png" height="80%" width="80%" alt="Windows 10 Steps"/>
<br>


<b>Test Internet Access</b> (Expected output: Successful replies, confirming NAT is working): <br/>
<img src="https://i.imgur.com/QpbcvpR.png" height="80%" width="80%" alt="Windows 10 Steps"/>
<br>
</p>

<p>
 <h2>📌 Conclusion</h2>
This lab provides hands-on experience in Active Directory administration, covering essential concepts such as domain setup, DHCP, NAT, PowerShell automation, and domain user management. By completing this setup, you gain practical knowledge that can be applied in real-world IT environments.

✅ Now, your Active Directory home lab is fully operational! 🚀
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

