# Required configuration for VM export<a name="prepare-vm-image"></a>

Use the following guidelines to configure your VM before exporting it from the virtualization environment\.

**General**
+ Install the AWS CLI on the workstation you will use to issue import commands\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.
**Tip**  
In [ supported AWS Regions](https://docs.aws.amazon.com/cloudshell/latest/userguide/supported-aws-regions.html), you can also use [AWS CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html) for a browser\-based, pre\-authenticated shell that launches directly from the AWS Management Console\.
+ Disable any antivirus or intrusion detection software on your VM\. These services can be re\-enabled after the import process is complete\.
+ Uninstall the VMware Tools from your VMware VM\.
+ Disconnect any CD\-ROM drives \(virtual or physical\)\.
+ Your source VM must have a functional DHCP client service\. Ensure that the service can start and is not disabled administratively\. All static IP addresses currently assigned to the source VM are removed during import\. When your imported instance is launched in an Amazon VPC, it receives a primary private IP address from the IPv4 address range of the subnet\. If you don't specify a primary private IP address when you launch the instance, we select an available IP address in the subnet's IPv4 range for you\. For more information, see [VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing)\.
+ Shut down your VM before exporting it\.

**Windows**
+ Enable Remote Desktop \(RDP\) for remote access\.
+ Make sure that your host firewall \(Windows firewall or similar\), if configured, allows access to RDP\. Otherwise, you cannot access your instance after the import is complete\.
+ Make sure that the administrator account and all other user accounts use secure passwords\. All accounts must have passwords or the importation might fail\.
+ Install \.NET Framework 4\.5 or later on the VM\. We install the \.NET framework on your VM as needed\.
+ You can run System Preparation \(Sysprep\) on your Windows Server VM images before or after they are imported\. If you run Sysprep before importing your VM, the importation process adds an answer file \(`unattend.xml`\) to the VM that automatically accepts the End User License Agreement \(EULA\) and sets the locale to EN\-US\. If you choose to run Sysprep after importation, we recommend that you use EC2Launch \(Windows Server 2016 and later\) or EC2Config \(through Windows Server 2012 R2\) to run Sysprep\.

**To include your own answer file instead of the default \(unattend\.xml\)**

  1. Copy the following sample file below and set the **processorArchitecture** parameter to **x86** or **amd64**, depending on your OS architecture:

     ```
     <?xml version='1.0' encoding='UTF-8'?>
     <unattend xmlns:wcm='http://schemas.microsoft.com/WMIConfig/2002/State' xmlns='urn:schemas-microsoft-com:unattend'>
      <settings pass='oobeSystem'>
       <component versionScope='nonSxS' processorArchitecture='x86 or amd64' name='Microsoft-Windows-International-Core' publicKeyToken='31bf3856ad364e35' language='neutral'>
        <InputLocale>en-US</InputLocale>
        <SystemLocale>en-US</SystemLocale>
        <UILanguage>en-US</UILanguage>
        <UserLocale>en-US</UserLocale>
       </component> 
       <component versionScope='nonSxS' processorArchitecture='x86 or amd64' name='Microsoft-Windows-Shell-Setup' publicKeyToken='31bf3856ad364e35' language='neutral'>
        <OOBE>
         <HideEULAPage>true</HideEULAPage>
         <SkipMachineOOBE>true</SkipMachineOOBE>
         <SkipUserOOBE>true</SkipUserOOBE>
        </OOBE>
       </component>
      </settings>
     </unattend>
     ```

  1. Save the file in the `C:\Windows\Panther` directory with the name `unattend.xml`\.

  1. Run Sysprep with the /oobe and /generalize options\. These options strip all unique system information from the Windows installation and prompt you to reset the administrator password\.

  1. Shut down the VM and export it from your virtualization environment\.
+ Disable Autologon on your Windows VM\.
+ Open **Control Panel** > **System and Security** > **Windows Update**\. In the left pane, choose **Change settings**\. Choose the desired setting\. Be aware that if you choose **Download updates but let me choose whether to install them** \(the default value\) the update check can temporarily consume between 50% and 99% of CPU resources on the instance\. The check usually occurs several minutes after the instance starts\. Make sure that there are no pending Microsoft updates, and that the computer is not set to install software when it reboots\.
+ Apply the following hot fixes as needed:
  + [You cannot change system time if RealTimeIsUniversal registry entry is enabled in Windows](https://support.microsoft.com/en-us/help/2922223/you-cannot-change-system-time-if-realtimeisuniversal-registry-entry-is)
  + [High CPU usage during DST changeover in Windows Server 2008, Windows 7, or Windows Server 2008 R2](https://support.microsoft.com/en-us/help/2800213/high-cpu-usage-during-dst-changeover-in-windows-server-2008-windows-7)
+ Set the RealTimeIsUniversal registry key\. For more information, see [Setting the Time](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Linux**
+ Enable Secure Shell \(SSH\) for remote access\.
+ Make sure that your host firewall \(such as Linux iptables\) allows access to SSH\. Otherwise, you won't be able to access your instance after the import is complete\.
+ Make sure that you have configured a non\-root user to use public key\-based SSH to access your instance after it is imported\. The use of password\-based SSH and root login over SSH are both possible, but not recommended\. The use of public keys and a non\-root user is recommended because it is more secure\. VM Import does not configure an `ec2-user` account as part of the import process\.
+ Make sure that your Linux VM uses GRUB \(GRUB legacy\) or GRUB 2 as its bootloader\.
+ Make sure that your Linux VM uses one of the following for the root file system: EXT2, EXT3, EXT4, Btrfs, JFS, or XFS\.