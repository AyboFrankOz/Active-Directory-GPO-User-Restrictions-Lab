# Active-Directory-GPO-User-Restrictions-Lab
In this lab, I demonstrate how to use Group Policy Objects (GPOs) on Windows Server 2022 to enforce enterprise-style restrictions on a domain-joined workstation, including hiding system icons, enforcing a corporate wallpaper, blocking Control Panel, Settings, Command Prompt, PowerShell, software uninstallation, network settings, and USB drives. Using loopback processing (Merge mode) and proper security filtering, user restrictions are applied based on the computer context, while administrative accounts remain unaffected. This lab showcases real-world GPO management, layered restrictions, and troubleshooting techniques to ensure policies are correctly enforced.

A Group Policy Object (GPO) is a collection of policy settings that define how computers and users behave within a Windows domain environment. Companies use GPOs to enforce security, standardize settings, restrict unauthorized actions, deploy software, and automate management across all workstations efficiently.

To provide a detailed point of view, in this lab, I implement strict endpoint restrictions for all computers within the UserPCs OU using Group Policy. Any user from departments such as Finance, HR, Marketing, Operations, Sales, Security, and Technology will have these restrictions applied when logging into these machines. However, Executives and IT personnel are explicitly excluded, ensuring they retain full access on the same machines. This demonstrates a real-world approach to applying security policies while maintaining necessary administrative privileges.

First, we need to create the GPO. From Server Manager Dashboard > Tools > Group Policy Management. Find the UserPCs OU from the list, as we will implement the GPO here. Right-click> Create GPO in this domain and Link it here.
![Creating GPO](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/852cc572a2de212cc4eddd07fdd888af4e21182f/images/creating%20GPO%20(1).PNG)

Give it a name. I named it "User Restriction."
![Creating GPO](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/852cc572a2de212cc4eddd07fdd888af4e21182f/images/creating%20GPO%20(2).PNG)

Right-click > Edit to open Group Policy Management Editor.
![Creating GPO](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/852cc572a2de212cc4eddd07fdd888af4e21182f/images/creating%20GPO%20(3).PNG)

## 1. Hide Recycle Bin
User Configuration > Policies > Administrative Templates > Double-click Desktop folder. Find "Remove Recycle Bin icon from desktop". Notice that the state is "Not configured". Double-click to edit.
![Recyclebin](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/2e6799650b3b444729123ecc7a33c5a28313febb/images/recycle%20bin%20(1).PNG)

Set to "Enabled" > Apply > OK. Once you've done it, you will see that the state changes into "Enabled".
![Recyclebin](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/2e6799650b3b444729123ecc7a33c5a28313febb/images/recycle%20bin%20(2).PNG)

The Recycle Bin is still accessible via File Explorer even if hidden from the desktop. Hiding the Recycle Bin is a common customization in managed environments, reinforcing the idea of a restricted, policy-driven environment, where users have only the access they truly need. 

## 2. Set Desktop Wallpaper
Download an image that you want to set as a wallpaper and put it in a shared folder. I used <a href="https://www.freepik.com/free-photo/man-jumping-impossible-possible-cliff-sunset-background-business-concept-idea_1151016.htm#fromView=search&page=1&position=3&uuid=4c74c86e-60b3-443e-b29d-ca291099a0a3&query=motivational+wallpaper+impossible">Image by jigsawstocker on Freepik</a>, renamed it as "users.jpg" and shared the image through the Deployment$ folder, which was created in the previous lab: [Software-Deployment-with-Group-Policy](https://github.com/AyboFrankOz/Software-Deployment-with-Group-Policy) 
![Wallpaper](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/95285dc23780e2f1342caff79841799f2be465ec/images/gpos%20(0).PNG)

Go back to Group Policy Management Editor. User Configuration > Policies > Administrative Templates > Desktop > Desktop. Double-click on "Desktop Wallpaper"
![Wallpaper](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(1).PNG)

Click on "Enabled". Type the path for the image; in our case, it is ```\\DC01\Deployment$\users.jpg``` Choose "Fill" for the Wallpaper style.
![Wallpaper](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(2).PNG)

As we set a wallpaper, we don't want the users to change it. User Configuration > Policies > Administrative Templates > Control Panel > Personalization
![Wallpaper](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(3).PNG)

We will enable "Prevent changing theme", "Prevent changing color and appearance", and "Prevent changing desktop background".
![Wallpaper](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(4).PNG)

## 3. Block Control Panel & Settings
To maintain system stability and security, users are restricted from changing system settings or uninstalling applications. This is enforced by blocking access to the Control Panel and system settings, ensuring that critical configurations remain consistent and protected.

User Configuration > Policies > Administrative Templates > Control Panel > Enable: "Prohibit access to Control Panel and PC settings".
![Control Panel](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(5).PNG)

## 4. Block the Command Prompt and Registry Editing Tools
User Configuration > Policies > Administrative Templates > System. We will enable "Prevent access to the command prompt" and "Prevent access to registry editing tools".
![CMD and Regedit](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(6).PNG)

In addition, we can also block PowerShell, but we will do that in a future lab with AppLocker. 

## 5. Block Network Settings
User Configuration > Policies > Administrative Templates > Network > Network Connections
We will enable "Prohibit access to properties of a LAN connection".
![Network](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(7).PNG)

## 6. Block USB & External Drives
Blocking USBs and external hard drives is a very strong and common security practice in enterprise environments. Removable media can easily be used to copy sensitive company data or introduce malicious software into the system, bypassing traditional network defenses. Thus, it significantly reduces the risk of data breaches and malware infections. It also helps enforce data control and compliance policies, ensuring that confidential information stays within authorized systems. Overall, restricting external storage devices strengthens endpoint security and minimizes human error or insider threats.

To block USBs and external hard drives: User Configuration > Policies > Administrative Templates > System > Removable Storage Access > Enable: "All Removable Storage classes: Deny all access".
![USB](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/cd8381e40ddb3096f57b423c7dc30d755717e92b/images/gpos%20(8).PNG)

## 7. Pre-Deployment
So far, we have only enabled user-based configurations for this GPO. If we deploy it, it will not work as "UserPCs" OU contains only computer objects (only PC1 in our case). Since user policies apply based on user location in Active Directory, the GPO will not be processed. This issue would be resolved by either moving a user object into this OU or enabling loopback processing to apply user policies based on the computer context. As we want user policies to apply to computers, we will use **Loopback Processing**.

Computer Configuration> Administrative Templates > System > Group Policy. Double-click.
![Loopback](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/d9355a3697f37799775d3f66013cc56b6d9cb8f8/images/loopback%20(1).PNG)

Check "Enabled". Select "Merge" for the mode option. Apply and OK.
![Loopback](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/d9355a3697f37799775d3f66013cc56b6d9cb8f8/images/loopback%20(2).PNG)

Right now, if we deploy this GPO, any user who logs on to this computer (PC1) will be affected by these restrictions: Recycle Bin hidden, Control Panel and Settings blocked, CMD and Registry Editing Tools blocked, Network settings restricted, and USB drives disabled. So, exclusions for Executives and IT personnel must be configured to maintain administrative access. 

In this lab environment, Frank (our user with admin rights) is located under the "Admins" OU. Therefore, he won't be affected when we target the "All Employees" group.  
![Users](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/f256c72525fec280d716372f2eb99ac808136a5c/images/users.PNG)

Return to Groups Policy Management. Find the GPO, "PC1Restriction" in this case, double-click it. Under the "Scope" tab, find "Security Filtering". Remove "authenticated Users" as it covers all the users in the domain.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(1).PNG)

Click on the "Add" button, type "All Employees", and click OK to add the users. Now, all users under "All Employees" will be affected. However, we want to give "Executives" exclusions. 
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(2).PNG)

Click on the "Delegation" tab, then the "Advanced" button.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(6).PNG)

Click on the "Add" button, type "Executive" in the new window, and click OK.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(3).PNG)

Then, click on "Executive". Scroll down to find "Apply group policy" permission and check "Deny". This will give the exclusion to the Executive group.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(4).PNG)

If we check the same permission for the "All Employees", we can see that the Apply Group Policy permission is allowed.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/0e5588f1ffeb0657f6d3b10b830c888281472230/images/security%20filtering%20(5).PNG)

Lastly, we need to add "Domain Computers" as well, so computers can read this GPO. Scope tab > Add > Type "Domain Computers" and OK.
![Security Filtering](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/a8e9d39105cc36f19f984e43a99451f30e0a6548/images/security%20filtering%20(7).PNG)

## 8.Deployment and Test

To deploy the GPO, type ```gpupdate /force``` in CMD and restart the PC.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(1).PNG)

Let's log in with Bobby Drake (Username: Iceman) – Creative Marketing Director from the Marketing Department.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(2).PNG)

Recycle Bin is hidden, and the Wallpaper was changed.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(3).PNG)

When we want to change Display Settings...
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(4).PNG)

... it is blocked.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(5).PNG)

When we want to "Personalise", it is blocked.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(6).PNG)

Control panel...
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(7).PNG)

...is blocked.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(8).PNG)

CMD ...
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(9).PNG)

...is blocked.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(10).PNG)

When we want to open "Network Settings"...
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(12).PNG)

...nothing happens
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(3).PNG)

However, if we log in with Hank McCoy (Username: Beast), Chief Technology Officer from the Executive department.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(11).PNG)

Wallpaper is the default, and the recycle bin is not hidden.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(13).PNG)

We can access CMD, Registry Editor, and Control Panel.
![Testing](https://github.com/AyboFrankOz/Active-Directory-GPO-User-Restrictions-Lab/blob/ef970e355b9882430d57a150d6cb969dd2b6f40c/images/test%20(14).PNG)

## Conclusion

This lab demonstrates how **Group Policy Objects (GPOs)** can be used to enforce security, standardization, and administrative control within a Windows domain environment. A key challenge in this lab involved configuring the GPO application with **Loopback Processing**, which is essential for applying user restrictions based on the computer context. Furthermore, executive accounts were excluded from these restrictions using GPO delegation with deny permissions, demonstrating a real-world approach to maintaining security for standard users while preserving full access for privileged accounts. This lab can be extended further to simulate a more advanced enterprise environment, such as implementing AppLocker for application whitelisting and applying different GPO rules for specific departments or roles, and enforcing more granular security and compliance policies across the network.
