# Vulnerable Active Directory Homelab Building

Here is a walkthrough of how I built my vulnerable Active Directory domain, hosted locally on a Windows host using VirtualBox with Windows Server 2025 & Windows 11 Enterprise VMs.

## Domain Architecture
![AD_Domain](../Images/Vuln_AD_Lab.png)

## Getting Started

To begin, download the ISO files for your Domain Controller (Windows Server 2025) and user machines (Windows 11 Enterprise). You can find these files from Microsoft's [Evaluation Center](https://www.microsoft.com/en-us/evalcenter).

## Configuring the Domain Controller

### Creating the Windows Server 2025 Virtual Machine
On VirtualBox, select the **New** button to create a new virtual machine.

Give the VM a name and select the **Windows Server 2025 ISO image** you just downloaded. Depending if you're using **Basic** or **Expert** mode, you can also specify the **Type** as Microsoft Windows and the **Version** as Windows Server 2025 (64-bit). Also select **Skip Unattended Installation**.

Next is the machine's **Hardware** settings. I used the following:
* 5GB RAM (5120 MB)
* 2 CPUs
* Enabled EFI

**Note: Use as much or as little RAM for this machine (and others) as your PC can afford to allocate, however less RAM means the machine may run slower.**

For the **Virtual Hard disk**, I chose **Create a Virtual Hard Disk Now** at the default size of 50GB. I did **not** choose to pre-allocate the Full Size, and also left the options *Use an Existing Virtual Hard Disk File* and *Do Not Add a Virtual Hard Disk* un-checked.

Your new VM's summary of settings should be similar to this:

![ServerVM_summary](../Images/Server_VM_summary.PNG)

### Boot the Machine and Finish Setting up the OS Settings

Now **Start** your new VM and immediately press any key for it to boot, then follow the **Windows Server Setup** wizard:
* Select the language and time/currency format you desire
* Select your preferred keyboard/input method
* On the **Select setup option** screen, choose **Install Windows Server** and check the box agreeing that everything will be deleted from the VM during this process
* Select **Windows Server 2025 Standard Evaluation (Desktop Experience)** from the list of images
* Accept the EULA
* Select **Disk 0 Unallocated Space** for the location to install Windows Server

Your **Ready to install** screen should look like this:

![Server_ready](../Images/Server_VM_ready.PNG)

Now click **Install** and the Windows Server OS will begin installing on the VM.

![Server_install](../Images/Server_VM_install.PNG)

### Log in and Install VirtualBox Guest Additions

Once the OS finishes installing, you will be prompted to create a password for the built-in administrator account. This will be the password for your **Domain Administrator**.

In order to log on to the machine, we need to press Ctrl+Alt+Del. We can do this by clicking the **Input** tab, then **Keyboard > Insert Ctrl-Alt-Del**.

![Server_unlock](../Images/Server_unlock.PNG)

After logging in, you have to select the type of diagnostic data to send to Microsoft. After clicking **Accept** on that screen now you should see the Server Manager Dashboard.

![Server_dashboard](../Images/Server_dashboard.PNG)

To install VirtualBox's Guest Additions on the VM, click the **Devices** tab then select **Insert Guest Additions CD Image...**. This will open a file explorer window - open **This PC** and double-click the CD drive VirtualBox Guest Additions.

Inside the CD Drive, scroll until you find **VBoxWindowsAdditions-amd64** (or whichever architecture matches your machine) out of the list. Double-clicking it will run the install wizard. Follow it keeping all settings as their default values.

![Server_guest](../Images/Server_guest_adds.PNG)

### Make the Server your Domain Controller and Configure Active Directory Domain Services 

Before continuing, change the machine's name. Search **name** in the search bar and open **View your PC name**. When on the System > About page, click the button **Rename this PC** and enter the new name you want to use. For simplicity, be sure to use 'DC' in the name so you know this machine is the domain controller.

![Server_name](../Images/Server_change_name.PNG)

The machine will need to be rebooted to apply the name change. Restart it now.

Back in the Server Manager Dashboard, click the **Manage** tab then **Add roles and features**. You want to start a **Role-based or feature based installation**.

![Server_installType](../Images/Server_installType.PNG)

Be sure that your current Domain Controller is highlighted to select as the **destination server**.

![Server_destination](../Images/Server_destination.PNG)

Out of the list of server roles, select **Active Directory Domain Services**.

![Server_roles](../Images/Server_roles.PNG)

Now we need to click the **Add Features** button to add the required features for the Active Directory Domain Services role.

![Server_features](../Images/Server_features.PNG)

Now **click next twice** to skip adding more features or adding/installing anything related to Azure Active Directory.

Confirm your installation selections and check the box **Restart the destination server automatically if required**.

![Server_confirm](../Images/Server_confirmInstall.PNG)

Click the **Install** button and wait for the installation to complete. Once finished, click the link **Promote this server to a domain controller** to continue configuring the domain.

![Server_[promote]](../Images/Server_promote.PNG)

Now select the deployment operation **Add a new forest** and provide your root domain name.

![Server_newForest](../Images/Server_newForest.PNG)

Make sure your new forest and domain functional level are both listed as Windows Server 2025 and create a password for Directory Services Restore Mode. This password **must contain a combination of uppercase and lowercase letters, numbers, and symbols**.

![Server_DCoptions](../Images/Server_DCoptions.PNG)

Click **next** through the DNS Options - **do not create a DNS relegation**.

Under Additional Options, wait until the NetBIOS domain name field automatically populates.

![Server_addOptions](../Images/Server_addOptions.PNG)

Click **next** through the Paths - **leave all as their default values**.

![Server_paths](../Images/Server_paths.PNG)

Review the list of selection options to configure on the machine and click **next**.

![Server_review](../Images/Server_review.PNG)

Now you need to wait for the prerequisites check to complete. Once finished, click **Install**.

![Server_prereq](../Images/Server_prereq.PNG)

Let the machine automatically reboot. Now log back in using the **Domain Administrator** password created earlier.

### Configure Active Directory Certificate Services

In the Server Manager Dashboard, click **Manage > Add Roles and Features**.

Click **next** to pass the *Before You Begin* screen and select another **Role-based or feature-based installation**.

Make sure your Domain Controller is chosen as the destination server and click **next**.

![Server_certDest](../Images/Server_certDest.PNG)

Check the box to install the **Active Directory Certificate Services** then click **Add Features**.

![Server_certRole](../Images/Server_certRole.PNG)

![Server_certFeat](../Images/Server_addCertFeat.PNG)

Click **next** since you do not need to add any more features, and click **next** through AD CS.

Be sure to select the **Certication Authority** role service then click **next**.

![Server_certAuth](../Images/Server_certAuth.PNG)

Confirm your installation selections, check the box **Restart the destination server automatically if required**.

![Server_certConfirm](../Images/Server_certConfirm.PNG)

Click the **Install** button and wait for the installation to complete. Once finished, click the link **Configure Active Directory Certificate Services on the destination server**.

![Server_certInstall](../Images/Server_certInstall.PNG)

Make sure your **DOMAIN\Administrator** is the set of credentials used to configure role services, then click **next**.

![Server_certCreds](../Images/Server_certCreds.PNG)

Select the **Certification Authority** role service to configure then click **next**.

![Server_certAuthRole](../Images/Server_certAuthRole.PNG)

Choose to setup an **Enterprise CA** type then click **next**.

![Server_certEntCA](../Images/Server_certEntCA.PNG)

Choose to setup the **Root CA** of the domain then click **next**.

![Server_certCAtype](../Images/Server_certCAtype.PNG)

Choose to **Create a new private key** then click **next**.

![Server_privatekey](../Images/Server_privatekey.PNG)

Leave the **cryptographic options** as their default values then click **next**.

![Server_CAcrypto](../Images/Server_CAcrypto.PNG)

Leave the **CA name** as the default values and click **next**.

![Server_CAname](../Images/Server_CAname.PNG)

Choose how long you would prefer the **validity period** to be then click **next**.

![Server_certValidity](../Images/Server_certValidity.PNG)

Leave the **CA database location** to the default value and click **next**.

![Server_certCAdb](../Images/Server_certCAdb.PNG)

Confirm the list of configurations to apply, then click **Configure**.

![Server_certCAconfirm](../Images/Server_certCAconfirm.PNG)

Once the configuration is successful, reboot the machine to apply the changes.

![Server_certConfig](../Images/Server_certConfig.PNG)

## Configuring the User Machines

You will need to create two identical user machines. Repeat these steps twice to configure both.

On VirtualBox, select the **New** button to create a new virtual machine.

Give the VM a name and select the **Windows 11 Enterprise ISO image** you downloaded earlier. Depending if you're using **Basic** or **Expert** mode, you can also specify the **Type** as Microsoft Windows and the **Version** as Windows 11 (64-bit). Also select **Skip Unattended Installation**.

Next is the machine's **Hardware** settings. I used the following:
* 4GB RAM (4096 MB)
* 2 CPUs
* Enabled EFI

**Note: The user machines do not need as much RAM as the Windows Server needs.**

For the **Virtual Hard disk**, I chose **Create a Virtual Hard Disk Now** sized 55GB. The hard disks need to be **at least 52GB** in order for the Windows 11 OS to install. I did **not** choose to pre-allocate the Full Size, and also left the options *Use an Existing Virtual Hard Disk File* and *Do Not Add a Virtual Hard Disk* un-checked.

Your new VM's summary of settings should be similar to this:

![User_machineSummary](../Images/User_machineSummary.PNG)

Now **Start** your new VM and immediately press any key for it to boot, then follow the **Windows 11 Setup** wizard:
* Select the language and time/currency format you desire
* Select your preferred keyboard/input method
* On the **Select setup option** screen, choose **Install Windows 11** and check the box agreeing that everything will be deleted from the VM during this process
* Select **Windows 11 Enterprise Evaluation** from the list of images
* Accept the EULA
* Select **Disk 0 Unallocated Space** for the location to install Windows Server

Your **Ready to install** screen should look like this:

![User_machineInstall](../Images/User_machineInstall.PNG)

Now click **Install** and the Windows 11 OS will begin installing on the VM.

After the OS installation finishes, you need to configure a few machine settings:
* Select your preferred country/region
* Select your preferred keyboard layout/input method
* Skip adding a second keyboard layout if desired

When prompted to **set things up for work or school**, click the **Sign-in options** link then select **Domain join instead**.

![User_machineSignInOpt](../Images/User_machineSignInOpt.PNG)

![User_machineDomainJoin](../Images/User_machineDomainJoin.PNG)

Enter the local account's name to create, then click **next**.

![User_machineAcct](../Images/User_machineAcct.PNG)

Create the password for this machine's local account, then create the 3 security questions required.

Select your preferred **privacy settings** then click **Accept**.

![User_machinePrivacy](../Images/User_machinePrivacy.PNG)

Wait until the machine updates finish downloading and installing.

![User_machineUpdates](../Images/User_machineUpdates.PNG)

Change the machine's name just like you did for the Windows Server. Search **name** in the search bar and open **View your PC name**. When on the System > About page, click the button **Rename this PC** and enter the new name you want to use. 

### Install VirtualBox Guest Additions

To install VirtualBox's Guest Additions on the VM, click the **Devices** tab then select **Insert Guest Additions CD Image...**. This will open a file explorer window - open **This PC** and double-click the CD drive VirtualBox Guest Additions.

Inside the CD Drive, scroll until you find **VBoxWindowsAdditions** out of the list. Double-clicking it will run the install wizard. Follow it keeping all settings as their default values.

Reboot the Windows 11 VM to apply these changes.

## Configuring Users, Groups and Policies

**Do not start this until both Windows 11 VMs are created and fully configured.**

At this step you can shut down the Windows 11 VMs if either are running. Boot your Domain Controller (Windows Server VM) to begin the next configuration steps.

On the Server Manager Dashboard, click **Tools > Active Directory Users and Computers**. Right click your **DOMAIN.local** entry in the left-hand listing and click **New > Organizational Unit**.

![createNewOU](../Images/createNewOU.PNG)

Name the new OU **Groups** and make sure the box **Protect container from accidental deletion** is checked, then click **OK**.

![newOUName](../Images/newOUName.PNG)

Open the list of **Users** in the left-hand listing and highlight all the Security Groups in the list. Move them into the new **Groups OU** and click **Yes** when asked if you're sure about moving the object.

![moveSecGroups](../Images/moveSecGroups.PNG)

Now you should only see the Administrator and Guest accounts in the list of Users.

![usersList](../Images/usersList.PNG)

### Create a new Administrator User

Right click the existing **Administrator** user and select **copy** to create a new user with the same level of Domain Administrator permissions.

![copyAdminUser](../Images/copyAdminUser.PNG)

Give this new user a First and Last name, Full name and a User logon name then click **Next**.

![copyAdminUserDetails](../Images/copyAdminUserDetails.PNG)

Create a password for this new user and make sure the box **Password never expires** is checked.

![copyAdminUserPW](../Images/copyAdminUserPW.PNG)

Confirm the new user's settings, then click **Finish** to create your new Admin user.

![copyAdminUserConfirm](../Images/copyAdminUserConfirm.PNG)

### Create a new Service Account having Domain Administrator Permissions

Like we just did with the last new user, copy the existing **Administrator** user account.

Name this new account something similar to SQL Service, since this new service account will be used to run an SQL server.

![serviceAcct](../Images/serviceAcct.PNG)

Create a password for the new service account, again making sure the box **Password never expires** is checked. Confirm the new service account's settings, then click **Finish** to create it.

Right click the new SQLService account in the list and select **Properties**. In the **Description** field, add what the account's password is. Then click **Apply** and **OK**.

![serviceAcctPWdescription](../Images/serviceAcctPWdescription.PNG)

### Create Two New Domain Users

Follow these steps twice to create two new low-level users in the domain.

Right-click in the empty space of the Users list and select **New > User**.

![newDomainUser](../Images/newDomainUser.PNG)

Give the new user a First and Last name, Full name and a User logon name then click **Next**. Create a password for this new user and make sure the box **Password never expires** is checked. Confirm the new user's settings, then click **Finish** to create your new user.

![newLowDomainUser](../Images/newLowDomainUser.PNG)

### Create a Network File Share

In the Server Manager Dashboard, select **File and Storage Services** in the left column, then **Shares**. Find the **TASKS** dropdown button and click **New Share...**.

![networkFileShare](../Images/networkFileShare.PNG)

Choose a **SMB Share - Quick** as the File share profile, then click **next**.

![SMBshare](../Images/SMBshare.PNG)

Leave the **Share Location** as the default value and click **next**.

![shareLocation](../Images/shareLocation.PNG)

Give your Share a name and click **next**. No need to edit the local and remote paths.

![shareName](../Images/shareName.PNG)

Leave the **Other Settings** to their default values, then click **next**.

![shareOtherSettings](../Images/shareOtherSettings.PNG)

Leave the **Permissions to control access** as their default values, then click **next**.

![sharePermissions](../Images/sharePermissions.PNG)

Confirm the Share configuration selections, then click **Create**.

![shareConfirm](../Images/shareConfirm.PNG)

Wait for the Share to be created, then close the Wizard.

![shareResults](../Images/shareResults.PNG)

### Register a Service Principal Name (SPN) for your Service Account

You must next register the SPN for your service account to enable Kerbero authentication for the SQLService running on a port you specify.

Open a new command prompt on the Windows Server and run is **as Administrator**. Use the command `setspn -a [DOMAIN CONTROLLER]/[SERVICE ACCT NAME].[DOMAIN NAME].local:60111 [DOMAIN NAME]\SQLService`.

![serviceRegSPN](../Images/serviceRegSPN.PNG)

Confirm this was successful by querying all the registered SPNs across your domain using the command `setspn -T [DOMAIN NAME].local -Q */*`. Look for the phrase **Existing SPN found!**.

![querySPN](../Images/querySPN.PNG)

### Create a new Group Policy for the Domain

Search for and open the **Group Policy Management** app. Expand the existing Forest: **[YOUR DOMAIN].local** and its **Domains**. Right click on **[YOUR DOMAIN].local** and select **Create a GPO in this domain, and Link it here...***.

![newGPO](../Images/newGPO.PNG)

Name the new GPO something similar to 'Disable Windows Defender', then click **OK**.

![GPOname](../Images/GPOname.PNG)

Right click on the new 'Disable Windows Defender' policy in the listing and click **Edit...**.

![editGPO](../Images/editGPO.PNG)

In the left-hand column, expand **Computer Configuration > Policies > Administrative Templates > Windows Components**. Find **Microsoft Defender Antivirus** in the list then double click **Turn off Microsoft Defender Antivirus**.

![defenderAntivirusOff](../Images/defenderAntivirusOff.PNG)

Click **Enabled** to enable the policy, then click **Apply** and **OK**.

![enableDefenderOff](../Images/enableDefenderOff.PNG)

Close the **Group Policy Management Editor** window. Under the [YOUR DOMAIN].local list, right click the **Disable Windows Defender** policy, then select **Enforced**.

![enforceDisableDefender](../Images/enforceDisableDefender.PNG)

You can confirm the policy is now enabled and enforced by checking that **Enforced = Yes** and **Link Enabled = Yes**.

![confirmDefenderDisable](../Images/confirmDefenderDisable.PNG)

## Join User Machines to the Domain

Before you continue adding the user machines (Windows 11 VMs) to your domain, first ensure the Domain Controller (Windows Server) and user machines are all connected to the same network.

Open the **Settings > Network** of each VM and make sure their network adapters are connected and attached to the same interface. I connected my VMs on a NAT Network. A quick tutorial on how to create a new NAT network on VirtualBox and attach your VMs to it can be found [here](https://www.youtube.com/watch?v=csuMaEsxp0A).

After connecting your VMs to the same network, boot all 3 VMs and make sure they all have an IP address. Do this by opening a command prompt and running the command `ipconfig`.

![ipconfig](../Images/ipconfig.PNG)

### Configure DNS Server IP on Each User Machine

Next, you need to configure your user machines to use your Domain Controller as their DNS server. Do the following steps on **both** user machines.

Search for and open **View Network Connections**. Double-click the **Ethernet** connection to configure it.

![ethernetConnection](../Images/ethernetConnection.PNG)

Then open **Properties** and double-click **TCP/IPv4 properties**.

![ethernetProperties](../Images/ethernetProperties.PNG)

Seldct **Use the following DNS server addresses** and enter the IP address of your Domain Controller, then click **OK**.

![ethernetDNS](../Images/ethernetDNS.PNG)

Now search for **domain** and open the **Access work or school** system setting.

![domainSearch](../Images/domainSearch.PNG)

Click the blue **Connect** button, then click the link **Join this device to a local Active Directory domain**.

![domainConnect](../Images/domainConnect.PNG)

![joinLocalDomain](../Images/joinLocalDomain.PNG)

Enter your domain name then click **Next**.

![joinDomainName](../Images/joinDomainName.PNG)

Choose the user name **administrator** and user your **Domain Administrator's** password, then click **OK**.

![joinDomainUser](../Images/joinDomainUser.PNG)

Add a user account **administrator** and account type of **Administrator**, then click **Next**.

![joinDomainAddAdmin](../Images/joinDomainAddAdmin.PNG)

Click **Restart now** to restart the machine and apply the changes.

### Confirm User Machines have Joined your Domain

On the domain controller, in the Server Manager Dashboard, click **Tools > Active Directory Users and Computers**. Then expand the list of **Computers** in the left-hand listing. You should see both user machines listed.

![usersJoinedDomain](../Images/usersJoinedDomain.PNG)

### Configure Local Administrator Accounts and Network Discovery on Both User Machines

Log on to as the **Domain Administrator** on the first VM (mine was named *Phantom*), search for **Users** and open **Edit local users and groups**.

![editUsersAndGroups](../Images/editUsersAndGroups.PNG)

Right click the local built-in **Administrator** and select **Set Password...**.

![setAdminPass](../Images/setAdminPass.PNG)

Configure and apply the account's password. Now double click on the **Administrator** account again and uncheck the **Account is disabled** box to enable the local Administrator account. Click **Apply** then **OK**.

![enableLocalAdmin](../Images/enableLocalAdmin.PNG)

You can confirm that the local Administrator account is now enabled because the account's icon no longer has the down arrow.

![confirmAdminEnabled](../Images/confirmAdminEnabled.PNG)

Now open the **Groups** list and double click the **Administrators** group. Here you can see the list of users who currently have Admin access on the computer/domain.

![localAdminList](../Images/localAdminList.PNG)

Click **Add** to add a new user to this Administrators group. Type the user login name for the user who was a copy of your Domain Administrator (mine was Velma Dinkley - vdinkley). Click the **Check Names** button, and this user's full object name will appear in the box. Then click **OK** then **Apply**.

![addNewLocalAdmin](../Images/addNewLocalAdmin.PNG)

You should now see this user in the list of Members in the **Administrators Properties** window.

One last configuration for this machine is to **turn on network discovery and file sharing**. Do this by opening a new **File Explorer** window and opening the **Network** tab.

You should see a message that the setting is currently turned off. Click this message then **Turn on network discovery and file sharing**.

![enableNetDiscovery](../Images/enableNetDiscovery.PNG)

You can confirm that the setting is now turned on by opening **Settings > Network & Internet > Advanced Network Settings > Advanced Sharing Settings**. 

![confirmNetDiscovery](../Images/confirmNetDiscovery.PNG)

Now this VM is fully configured. Boot your other Windows 11 user machine to apply these settings to it.

With your second user machine (mine is named *Scooby*), apply the following configurations:
* Set a password for the built-in Administrator user and enable it
* Edit the Administrators user group, this time adding 2 users:
  * 1 - the user copied from the Domain Administrator (mine was Velma Dinkley - vdinkley)
  * 2 - one of the low-level domain users (I chose my user Shaggy Rogers - srogers)
* Turn on network discovery and file sharing

The list of local administrators on your second user machine should look similar to this:

![scoobyLocalAdmins](../Images/scoobyLocalAdmins.PNG)

### Map the Network Drive on One User Machine 

Now we will configure one last setting on this same user machine: mapping the network drive. Begin by logging out of the **Domain Administrator** account currently logged in.

You will now log in as the original local account that was created when first installing the OS (my user was *Scooby*). Select **Other user** from the list to log in. Use the username **.\[USERNAME]** and their password.

![otherUserLogin](../Images/otherUserLogin.PNG)

Open a **File Explorer** window and go to **This PC** then click the **...** tab at the top and select **Map network drive**.

![mapNetDrive](../Images/mapNetDrive.PNG)

Apply the following settings:
* Leave the Drive as the default value
* Choose the `\\[DOMAIN CONTROLLER]\[NETWORK FILE SHARE]` folder
* Check the **Reconnect at sign-in** and **Connect using different credentials** boxes

![mapDriveSettings](../Images/mapDriveSettings.PNG)

Click **Finish** and add your **Domain Administrator's** credentials as the network credentials. Also check the **Remember my credentials** box.

![networkCreds](../Images/networkCreds.PNG)

Now you should see the network file share under the list of **Network locations** in the File Explorer.

![confirmMapNetDrive](../Images/confirmMapNetDrive.PNG)

**Congrats! You have now finished creating and configuring your vulnerable Active Directory domain. Your full architecture should look similar to image at the top of this README. Now you're ready to start attacking it.**