<img src="https://i.imgur.com/hdhcw65.png" height="40%" width="40%" alt="osticket"/>
<br />
<h1>On-Premises Active Directory Deployed in the Cloud (Azure)</h1>

<h2>Description</h2>
This lab outlines the implementation of on-premises Active Directory within two Azure virtual machines.
<br />

<h2>Operating Systems Used</h2>

- <b>Windows 10</b>
- <b>Windows Server 2022</b>
<h2>Environments and Technologies Used</h2>

- <b>Microsoft Azure</b>
- <b>Active Directory Domain Services</b>
- <b>PowerShell</b>
- <b>Remote Desktop</b>

<h2>Lab walk-through:</h2>

1. Setup Virtual Network and Virtual Machines<br />
Create Virtual Network: In Azure, create a virtual network named Active_Directory_VNet (AD_VNet).<br />
<br /><img src="https://i.imgur.com/EeuQ2Em.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
Create Virtual Machines:<br />
DC_1: Deploy a Windows Server 2022 VM for the Domain Controller (DC_1) within AD_VNet.<br />
<br /><img src="https://i.imgur.com/aW7bLWK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />Client_1: Deploy a Windows 10 Pro VM as a client machine (Client_1) within AD_VNet.<br/>
<br /><img src="https://i.imgur.com/sDuRo96.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
2. Configure DC_1 Network Settings<br />
Set DC_1's private IP address from dynamic to static.<br />
<br /><img src="https://i.imgur.com/eo5GVe6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
Disable Windows Firewall on DC_1:<br />
Log into DC_1.
Open Run > type wf.msc, hit Enter.
In the Windows Defender Firewall properties, select Turn off Windows Defender Firewall (Lab Purposes Only).
Apply the settings for both private and public networks.<br />
<br />
3. Configure DNS Settings on Client_1<br />
Set Client_1's DNS to the private IP address of DC_1.<br />
<br /><img src="https://i.imgur.com/3Ww1fqa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
4. Ping Test Between Client_1 and DC_1<br />
Attempt to ping DC_1 from Client_1.<br />
<br /><img src="https://i.imgur.com/qPQyzgE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
If the ping fails, check:
Both VMs are in the same VNet.
Firewall settings on DC_1.<br />
<br />Run the command ipconfig /all on Client_1 to verify the DNS server is set to DC_1's private IP.<br />
<br /><img src="https://i.imgur.com/2FrBRwQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
5. Install Active Directory Domain Services on DC_1<br />
Log into DC_1, open Server Manager.<br />
Navigate to Add Roles and Features > Select Active Directory Domain Services > Click Install.<br />
<br /><img src="https://i.imgur.com/TUEhDPo.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
6. Promote DC_1 to Domain Controller<br />
After installation, open Server Manager and click the notification to Promote this server to a domain controller.<br />
Select Add a new forest.<br />
Set the Root domain name as mydomain.com.<br />
Set a Directory Services Restore Mode (DSRM) password.<br />
Click Install. The server will automatically restart once the process is complete.<br />
<br /><img src="https://i.imgur.com/tbVuxZW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
7. Login to Client_1 as Domain User<br />
Test Log into Client_1 using the domain credentials mydomain.com(original username) and the password that was used to set up Client_1.<br />
<br />
8. Create Domain Admin User<br />
On DC_1, open Active Directory Users and Computers (ADUC).<br />
<br /><img src="https://i.imgur.com/WqLheV8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
Create an Organizational Unit (OU) named _EMPLOYEES and another OU named _ADMINS.<br />
<br /><img src="https://i.imgur.com/Vr1BVP2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
In the _ADMINS OU, create a new employee named Jane Doe, with username jane_admin and the same password as Client_1.<br />
<br /><img src="https://i.imgur.com/cCHLEFd.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
Add Jane Doe to the Domain Admins security group:<br />
Right-click the Jane Doe account > Go to Member Of tab > Click Add > Enter Domain Admins > Click Check Names > Apply changes.<br />
<br /><img src="https://i.imgur.com/OISyrgY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
9. Test Login as Jane Doe<br />
Log out of DC_1 and log back in as mydomain.com\jane_admin to verify admin rights.<br />
<br />
10. Join Client_1 to the Domain<br />
On Client_1, log in as the original user.<br />
Right-click the Start menu > Go to System > Click Rename this PC (advanced) > Under Computer Name > click Change.<br />
Select Member of and enter the domain "mydomain.com".<br />
Restart Client_1.<br />
<br /><img src="https://i.imgur.com/U83Seh8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
11. Verify Client_1 in ADUC<br />
On DC_1, in Active Directory Users and Computers (ADUC), go to the Computers folder and verify Client_1 appears.<br />
<br /><img src="https://i.imgur.com/fxJu9Qi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
12. Allow Remote Desktop for All Users on Client_1<br />
On Client_1, right-click the Start menu > Go to System > Remote Desktop > Click User Accounts > Add Domain Users to allow all users to access Remote Desktop.<br />
<br /><img src="https://i.imgur.com/bCcmm3b.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
13. Create Bulk Users using PowerShell<br />
On DC_1, open PowerShell_ISE as Administrator.<br />
Use <a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">script</a> to generate 1,000 users and paste it into the editor. Run the script.<br />
The new users will be populated under the _EMPLOYEES OU in ADUC.<br />
<br /><img src="https://i.imgur.com/NTnEgLm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
14. Configure Group Policy for Account Lockout<br />
On DC_1, open Group Policy Management (gpmc.msc).<br />
Right-click the Default Domain Policy > Edit<br />
Navigate to Computer Configuration > Policies > Windows Settings > Security Settings > Account Lockout Policy<br />
Set the Account lockout threshold to 5 invalid logon attempts<br />
<br /><img src="https://i.imgur.com/FYSstBJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
15. Force Group Policy Update<br />
On DC_1, open PowerShell and run the command gpupdate /force to apply the new policy immediately.<br />
<br /><img src="https://i.imgur.com/dDCuRbJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
16. Simulate Account Lockouts<br />
Log out of Client_1.<br />
Attempt to log in with a randomly generated employee account and input an incorrect password multiple times to simulate an account lockout.<br />
<br /><img src="https://i.imgur.com/P3VYUil.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
17. Unlock the Account<br />
On DC_1, go to ADUC > Find the locked employee account under _EMPLOYEES.<br />
Double-click the account > Go to the Account tab > Uncheck Unlock Account > Apply to unlock the account.<br />
<br /><img src="https://i.imgur.com/jSRNli3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
18. Reset Password, Disable, and Enable User Account<br />
While in the _EMPLOYEES OU, demonstrate how to:<br />
Reset the password for a user.<br />
<br /><img src="https://i.imgur.com/rwnSvCG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br /><img src="https://i.imgur.com/xfy4Oi9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
Disable and Enable the user account as needed.<br />
<br /><img src="https://i.imgur.com/SlTE6a2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br /><img src="https://i.imgur.com/FCgwzs3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
19. Observe Event Logs<br />
On Client_1, sign in as the original user.<br />
Open the Event Viewer (eventvwr.msc) > Go to Windows Logs > Security.<br />
Observe the events related to account lockouts, logon attempts, and other security-related activities.<br />
<br /><img src="https://i.imgur.com/3ZpiBe3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><br />
<br />
<br />
This completes the configuration of Active Directory within Azure Virtual Machines, setting up a domain, user management, group policies, and security measures like account lockouts.
