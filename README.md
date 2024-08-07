# Cybersecurity-Home-Lab-with-Active-Directory

## Background

In a corporate network environment, I designed and implemented a virtualized network infrastructure using Oracle VM, simulating a typical enterprise setup. This involved the installation and configuration of Microsoft Server 2019 with various network services such as Active Directory Domain Services (AD DS), DHCP, and Routing and Remote Access Service (RRAS), and a Windows 10 client machine. The project demonstrated how these components interconnect to form a cohesive enterprise network, providing centralized management through Active Directory Domain Services (AD DS) and remote access capabilities.

## Tools used
- Oracle VM
- Power Shell

## Description
-	Downloaded Windows Server 2019 iso image and Windows 10 iso image. 
-	Launched the Virtual Box and added a machine to host Windows Server 2019. Configure the network adapter settings, adapter 1 using NAT (to connect to my home internet) and adapter 2 using INTERNAL NETWORK via remote access (the client machine would be connected to this network, preventing direct interfacing with the external network), then launched the machine to install the Windows Server 2019.
-	When the machine was done installing the Windows Server 2019, I used a general password value "Password1" (Since it is a lab in a virtual environment using such a weak password does not really matter) which is the default password for all accounts to finish setting up the administrator account.
-	I renamed the Network adapters on the server, the NAT which connects to the outside network was renamed _INTERNET_ while the INTERNAL NETWORK was renamed x_INTERNAL_x. I configured the IP address of the x_INTERNAL_x using the parameters on the active directory screenshot.

<p align="center">
<img src="https://imgur.com/uaDFQ9e.png" height="50%" width="40%"/>
</p>

-	I renamed the Windows Server Machine as DC. From the VM machine device drop list, I clicked on (Insert Guest Addition CD Image), navigated to My PC on File Explorer, opened the downloaded file and double-clicked on (VBoxWindowsAdditions-amd64) to install the guest addition cd image. The purpose of doing this is to enhance navigation. I manually shut down the machine after the installation to effect all the changes I have made.
-	The next step is the Active Directory installation on the Server acting as my Domain Controller. This enables the addition of organizational units through which users and devices can be added and managed.
-	First, in the Server Manager Window, I clicked on (Add Roles and Features). On the Server Destination dialogue box, ensure that the name of the server (DC) is selected then click on next. On the Select Server Role dialogue box click on Active Directory Domain Services and then installed.

<p align="center">
<img src="https://imgur.com/vB6Ul8L.png" height="50%" width="40%"/>
</p>

-	The next step is to promote the just-created Active Directory to a Domain Controller. On the Server Manager Window, I clicked on the flag icon on the top ribbon with a caution sign and clicked on Promote Server to a Domain Controller. On the Deployment Configuration dialogue box that appeared I clicked on add new forest then I entered my desired domain name (topzdomain.com). Clicked on next, entered the default password (Password1), and then installed it.
-	Next step is to create an organizational unit and add an admin user. From Windows administrative tools on the server, I launch the Active directory users and computers then on the active directory window, I right-clicked on my domain server name (topzdomain.com), clicked on new, organizational unit, filled in the Unit’s name (_ADMINS), and closed it when it was done. Then I right-clicked on the _ADMINS folder that was created, clicked new, user, entered my full name and set the new user login as (a_t.olajide), used the default password, set password to “password never expires” next, then finished. Then I right-clicked on the newly created user, clicked on all tasks, add to a group. In the text field on the pop-up dialogue box, typed in “Domain Admins”, clicked on check names, then ok since the name was found.

<p align="center">
<img src="https://imgur.com/uQKZMFe.png" height="40%" width="60%"/>
</p>

-	Next, I signed out from the default administrator account which the Microsoft server set-up initially and log in as other users using the newly created admin account credentials. With that, the active directory domain services installation has been completed. 
- The next step is to install RAS (Remote Access Services)/NAT (Network Address Translation) so that other Machines can connect to the Domain Controller. In the case of this lab, RAS/NAT would allow the Windows 10 client machine to connect to the server.
-	On the server management window, I clicked on add roles and features, I ensured that my domain server name was selected, clicked on next, on the select server roles, clicked on remote access then next, until I got to the select role services page clicked on routing, clicked on add features on the new pop-up dialogue box then next, until the install button became visible, then installed.
-	On the server management window, I clicked on tools, then routing and remote access. A new routing and remote access dialogue box appears. I right-clicked on my domain controller DC (local), clicked on configure routing and remote access. On the routing and remote access server set-up wizard, I clicked next, clicked on NAT network on the next page, clicked on the _INTERNET_ (it was because of this routing set-up that I renamed the server network adapters this way, so I can easily identify the out-facing network), then clicked on finish to complete the installation. The domain controller DC now has IPv4 and IPv6 routing and this completes the RAS/NAT configuration.

<p align="center">
<img src="https://i.imgur.com/jjDLBxk.png" height="40%" width="60%"/>
</p>
<h5 align="center"> Routing and remote access after configuaration</h5>

- The next step is to install the DHCP server and configure the DHCP. On the server management window, I clicked on add roles and features, on the pop-up dialogue box I clicked on next till I got to select destination server, I ensured that my domain server name was selected, clicked on next, on then selected server roles, I clicked on DHCP SERVER clicked on add features on the new pop-up dialogue box then next, until the install button became visible, then install. Once the installation is complete, I close the window. Now the DHCP server is installed, the next is to configure the DHCP.
-	On the server management window, I clicked on tools, scrolled down to and clicked DHCP and a new dhcp dialogue box appeared. On the dialogue box, I clicked on the fill-down icon on IPv4 to open it up, right-clicked on the IPv4, and clicked on New Scope. On the new scope wizard, I clicked on next, set the IP address scope range, start range, end range and subnet mask as contained in the active directory overview screenshot clicked next, next, on the Lease Duration page, I left the default duration of 8 days, next, next, on the Router (Default Gateway) page, I entered 172.16.0.1 which is the internal network’s IP address(it’ll serve as the gateway address for all devices on the internal network), clicked on Add, next, up to finish.

<p align="center">
<img src="https://imgur.com/BIuUp4d.png" height="50%" width="40%"/>
</p>

-	On the DHCP dialogue box that is still open, I right-clicked on dc.topzdomain.com (my domain controller), clicked on authorize, right-clicked on IPv4, and clicked on refresh. A green arrow appears near the IPv4 indicating the DHCP, Leasing and other services just installed are now fully active. Now, the next step is to create a thousand (1000) new users on the Domain Server. 
-	First, we would configure the local server to turn off Internet Explorer Enhanced Security Configuration. This is to prevent Internet Explorer from flagging security issues each time I try to browse the internet. 
-	The next step is to download the repository containing the code and name of users to be created from this link https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbTltWDBNTWlxa1NyRnBVZ2ZEbGo2RHZRSHJ4QXxBQ3Jtc0tuWXd4cUIwTE16MWdJM0VBQ0pIYzVyZXlEUmozTHVKQUJ6TDZtVEtqZUo3STJ0RTBOUHNiblFUYlJob2R5eWtEMldJMEtSb2N0bmZyNUxLcktuemZSUUFrSlFLY0RSWEVoeTlXN2p5bmtHd0tnNnVjRQ&q=https%3A%2F%2Fgithub.com%2Fjoshmadakor1%2FAD_PS%2Farchive%2Frefs%2Fheads%2Fmaster.zip&v=MHsI8hJmggI 
-	I downloaded and saved the file to the desktop folder on my domain server. Extracted the file on the desktop, then launched PowerShell ISE as administrator from the Windows button. On the power shell window, I clicked on the folder icon and navigated to the desktop where my extracted file is, then double-clicked the power shell code file. On the text file containing the names, I added my name to the top of the file and saved it.
-	On the command line, I input the following command “Set-ExecutionPolicy unrestricted” and click on Yes to All. This is to prevent any restrictions during code running. Next, is to navigate into the folder where the name.txt file is located through the commandline using the following code: “cd C:\Users\a_t.olajide\Desktop\AD_PS-master\AD_PS-master” Then I used ls to confirm the file was there, after which I ran the code for user creation. The next thing is to set up a machine with Windows 10 OS which is the client machine.

```commandline
PS C:\Windows\system32> Set-ExecutionPolicy unrestricted

PS C:\Windows\system32> cd C:\Users\a_t.olajide\Desktop\AD_PS-master\AD_PS-master

PS C:\Users\a_t.olajide\Desktop\AD_PS-master\AD_PS-master> ls


    Directory: C:\Users\a_t.olajide\Desktop\AD_PS-master\AD_PS-master


Mode                LastWriteTime         Length Name                                                                
----                -------------         ------ ----                                                                
-a----        1/25/2024   4:21 AM           1811 .gitignore                                                          
-a----        1/25/2024   4:21 AM           1025 1_CREATE_USERS.ps1                                                  
-a----        1/25/2024   4:21 AM           1532 Generate-Names-Create-Users.ps1                                     
-a----        1/25/2024   4:26 AM          15582 names.txt
```

<p align="center">
<img src="https://github.com/Topzdomain/Cybersecurity-Home-Lab-with-Active-Directory/blob/main/creating_1000_users_screenshot.png?raw=true" height="70%" width="60%"/>
</p>

-	On the virtual box, I created a new machine, set the network parameters to the Internal network, set base memory and CPU, double click to launch then navigated to the folder where the Windows 10 iso image was downloaded and rebooted the machine. After the installation, on the commandline, I entered the ipconfig command. This is to check and be sure that the configuration was successful and that the client machine can access the internet. After confirming that the IP, DNS and GATEWAY were properly configured, I pinged google and my domain server to confirm connectivity and it was successful.

<p align="center">
<img src="https://github.com/Topzdomain/Cybersecurity-Home-Lab-with-Active-Directory/blob/main/screenshot_showing_successful_connectin_on_the_client_machine.png?raw=true" height="70%" width="60%"/>
</p>

- The screenshot above shows the client machine (CLIENT1) successfully connecting to the domain controller, echo response from ping to Google shows that address routing is working and CLIENT1 can communicate with the external network through the domain controller. Also, if any of the one thousand (1000) usernames that were created in the active directory are used to log into the CLIENT1 machine. The login is successful, the user can access the internal server and also communicate with the external network through the domain controller. This goes to show that the whole set-up was very successful. 

Skills Gained:
- Virtualization: Setting up and managing virtual machines using Oracle VM.
- Operating Systems Installation: Proficient in installing and configuring server and client operating systems.
- Network Configuration: Knowledge of NAT, internal networking, and network adapters.
- Active Directory Management: Installing, configuring, and managing Active Directory Domain Services.
- Scripting and Automation: Using PowerShell for automation of user provisioning and other administrative tasks.
- Remote Access Configuration: Setting up and managing remote access services and DHCP.
- Domain Management: Adding and managing users and devices within a domain, ensuring secure and centralized management.
