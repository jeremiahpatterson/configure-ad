<p align="center">
<img src="https://i.imgur.com/RihG9JT.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022 Datacenter - x64 Gen2
- Windows 10 Enterprise, version 22H2 - x64 Gen2


<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1 - Create the Domain Controller(DC) VM
- Step 2 - Create Client VM
- Step 3 - Set the DC VM Private IP address to static
- Step 4 - Set the Client's DNS setting to the DC VM private IP address.
- Step 5 - From the Client VM, attempt to ping the DC VM private address. Ensure the ping succeeded.
- Step 6 - Install Active Directory on the DC VM and promote to a Domain Controller
- Step 7 - Create a Domain Admin User within the Domain
- Step 8 - Join Client to the domain
- Step 9 - Set up Remote Desktop for non-admin users within the Client
- Step 10 - (Optional) Create testing Users with PowerShell Script.
- Step 11 - Task Complete

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/LnI83gT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After creating the Resource Group, proceed to creating your virtual machine. Choose the Resource Group that you just created, name the Virtual Machine, choose the same region as your Resource Group, and choose a Windows Server that does not have Core in the name. In this example, we used Windows Server 2022 Datacenter. Choose the disk size, create the Administrator Account, Click next until you reach the Networking section.
</p>
<br />

<p>
<img src="https://i.imgur.com/pdB54k0.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once in the Networking Section, create a new Virtual Network by clicking Create new in the Virtual Network section, name the new VNet, press OK, followed by Review + Create. When on the next page, if the validation passes, click Create. Now repeat the process to create the Client VM, but use a non-server version of Windows. In this example, we used Windows 10 Enterprise, version 22H2 - x64 Gen2.
Also, when on the network page, choose the Virtual Network you created during the dc-1 VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/TGeysI5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we need to set the dc-1 VM private IP to Static. Enter the dc-1 VM on Azure > Network Settings > Network Interface/ IP configuration > ipconfig1. Select Static and Save.
</p>
<br />

<p>
<img src="https://i.imgur.com/vRRQPej.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Time to set the Client DNS settings. Enter the Client VM on Azure > Network Settings > Network Interface/ IP configuration > DNS servers. Select Custom and enter dc-1 private IP address then click Save.  
</p>
<br />

<p>
<img src="https://i.imgur.com/LvsK2Yt.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We now have to log in to the dc-1 VM on Windows and disable the Firewall. Search Windows Defender Firewall with Advanced Security. Click on Windows Defender Firewall Properties. Click on the Domain Profile, then select the Firewall state dropdown and change it to Off. Repeat "Firewall state dropdown and change it to Off" for the Private and Public Profile tabs, press OK.
</p>
<br />

<p>
<img src="https://i.imgur.com/4XM6tg8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Let's ping to check the connection between the client and dc-1. Copy the dc-1 public address from Azure, log in to the Client VM on Windows, and open PowerShell. Enter: ping (dc-1 private IP). In this example, I entered the command: ping 10.0.0.4. Replies are coming in; the ping is a success.
</p>
<br />

<p>
<img src="https://i.imgur.com/CV50yDH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Let's Install Active Directory. Return to the dc-1 vm on Windows, open the Server Manager > Add Roles and Features > Server Roles > check Active Directory Domain Service > Add Features > Confirmation > check Restart destination Server, then Install.
</p>
<br />

<p>
<img src="https://i.imgur.com/EAiHH9V.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Let's promote this server to a Domain Controller. After the install click on the flag at the top of the navigation section, then click on "Promote this server to a domain controller." Select Add a new forest, then enter the Root domain name. These can be A real DNS domain your company owns (best practice), an internal-only subdomain, or A non-public domain (NOT recommended anymore). Click next. 
</p>
<br />

<p>
<img src="https://i.imgur.com/LjNzFXD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Choose a password for the Directory Services Restore Mode. Click next until ¥ou reach Prerequisite Check, then click Install. Congratulations, the server is now a Domain Controller.
</p>
<br />

<p>
<img src="https://i.imgur.com/uxNWvYA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Let's create an Organizational Unit (OU). Click on Start > Windows Administrative Tools > Active Directory Users and Computers. Right-click on the Domain > New > Organizational Unit. Easy...Right? For this tutorial, we will create three (OU's) _EMPLOYEES, _ADMINS, AND _GROUPS.
</p>
<br />

<p>
<img src="https://i.imgur.com/uUSwzQ8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Time to create a Domain Admin User. In the Active Directory Users and Computers, for this example, right-click on the _EMPLOYEES folder > New > User. Fill in the user information and press Next.
</p>
<br />

<p>
<img src="https://i.imgur.com/W7tndzz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Make the User a Domain Admin. In the Active Directory Users and Computers, click on the _EMPLOYEES folder. Right-click on the User and select Properties. Select the Member Of tab, click Add, and type in Domain Admins. Apply then OK.
</p>
<br />

<p>
<img src="https://i.imgur.com/lvjQcdb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, let's join the Client to the Domain. Log in to the Client VM in Windows. Right-click Start menu > Go to Systems > About > Rename this PC (advanced) > Change > select Domain and enter the domain name > OK. Finally, enter the Domain Admin credentials.
</p>
<br />

<p>
<img src="https://i.imgur.com/O8wo5le.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Let's configure the Remote Desktop for Domain Users. In the Client VM in Windows, go to Settings > Remote Desktop > Select users that can remotely access this PC > Add > type in Domain Users > OK
</p>
<br />

<p>
<img src="https://i.imgur.com/JwJnhUl.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Optional: Add test users with PowerShell ISE script. Copy raw script code (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) > open PowerShell ISE on the dc-1 VM in Windows. Click New, paste the script, and click Run. The script will start generating users.
</p>
<br />
