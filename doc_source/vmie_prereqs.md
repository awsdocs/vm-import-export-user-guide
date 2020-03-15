# VM Import/Export Requirements<a name="vmie_prereqs"></a>

Before attempting to import a VM, take action as needed to meet the following requirements\. You may also need to prepare your AWS environment by creating a service account with appropriate permissions, and you must prepare your locally hosted VM so that it will be accessible once it is imported into AWS\.

**Important**  
For most VM import needs, we recommend that you use the AWS Server Migration Service\. AWS SMS automates the import process \(reducing the workload of migrating large VM infrastructures\), adds support for incremental updates of changing VMs, and converts your imported VMs into ready\-to\-use Amazon machine images \(AMIs\)\. To get started with AWS SMS, see [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)\.

**Contents**
+ [System Requirements](#prerequisites)
  + [Operating Systems](#vmimport-operating-systems)
  + [Image Formats](#vmimport-image-formats)
  + [Instance Types](#vmimport-instance-types)
  + [Volume Types and File Systems](#vmimport-volume-types)
+ [Licensing Options](#licensing)
  + [Licensing for Linux](#linux)
  + [Licensing for Windows](#windows)
+ [Limitations](#limitations-image)
+ [Required Permissions for IAM Users](#iam-permissions-image)
+ [Required Service Role](#vmimport-role)
+ [Required Configuration for VM Export](#prepare-vm-image)
+ [Programmatic Modifications to VMs](#import-modify-vm)

## System Requirements<a name="prerequisites"></a>

Before you begin, you must be aware of the operating systems and image formats that VM Import/Export supports, and understand the limitations on importing instances and volumes\.

### Operating Systems<a name="vmimport-operating-systems"></a>

The following operating systems can be imported to and exported from Amazon EC2\. For more information about whether a Region is enabled by default, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Linux Instances*\.

**Windows \(Regions enabled by default\)**
+ Microsoft Windows Server 2003 \(Standard, Datacenter, Enterprise\) with Service Pack 1 \(SP1\) or later \(32\- and 64\-bit\)
+ Microsoft Windows Server 2003 R2 \(Standard, Datacenter, Enterprise\) \(32\- and 64\-bit\)
+ Microsoft Windows Server 2008 \(Standard, Datacenter, Enterprise\) \(32\- and 64\-bit\)
+ Microsoft Windows Server 2008 R2 \(Standard, Web Server, Datacenter, Enterprise\) \(64\-bit only\)
+ Microsoft Windows Server 2012 \(Standard, Datacenter\) \(64\-bit only\)
+ Microsoft Windows Server 2012 R2 \(Standard, Datacenter\) \(64\-bit only\) \(Nano Server installation not supported\)
+ Microsoft Windows Server 2016 \(Standard, Datacenter\) \(64\-bit only\)
+ Microsoft Windows Server 1709 \(Standard, Datacenter\) \(64\-bit only\)
+ Microsoft Windows Server 1803 \(Standard, Datacenter\) \(64\-bit only\)
+ Microsoft Windows Server 2019 \(Standard, Datacenter\) \(64\-bit only\)
+ Microsoft Windows 7 \(Home, Professional, Enterprise, Ultimate\) \(US English\) \(32\- and 64\-bit\)
+ Microsoft Windows 8 \(Home, Professional, Enterprise\) \(US English\) \(32\- and 64\-bit\)
+ Microsoft Windows 8\.1 \(Professional, Enterprise\) \(US English\) \(64\-bit only\)
+ Microsoft Windows 10 \(Home, Professional, Enterprise, Education\) \(US English\) \(64\-bit only\)

**Windows \(Regions not enabled by default\) \(64\-bit only\)**
+ Microsoft Windows Server 2008 R2 \(Standard, Web Server, Datacenter, Enterprise\)
+ Microsoft Windows Server 2012 \(Standard, Datacenter\)
+ Microsoft Windows Server 2012 R2 \(Standard, Datacenter\) \(Nano Server installation not supported\)
+ Microsoft Windows Server 2016 \(Standard, Datacenter\)
+ Microsoft Windows Server 1709 \(Standard, Datacenter\)
+ Microsoft Windows Server 1803 \(Standard, Datacenter\)
+ Microsoft Windows Server 2019 \(Standard, Datacenter\)
+ Microsoft Windows 7 \(Home, Professional, Enterprise, Ultimate\) \(US English\)
+ Microsoft Windows 8 \(Home, Professional, Enterprise\) \(US English\)
+ Microsoft Windows 8\.1 \(Professional, Enterprise\) \(US English\)
+ Microsoft Windows 10 \(Home, Professional, Enterprise, Education\) \(US English\)

**Linux/Unix \(64\-bit only\)**
+ Ubuntu 12\.04, 12\.10, 13\.04, 13\.10, 14\.04, 14\.10, 15\.04, 16\.04, 16\.10, 17\.04, 18\.04
+ Red Hat Enterprise Linux \(RHEL\) 5\.1\-5\.11, 6\.1\-6\.9, 7\.0\-7\.6 \(6\.0 lacks required drivers\)
+ SUSE Linux Enterprise Server 11 with Service Pack 1 and kernel 2\.6\.32\.12\-0\.7
+ SUSE Linux Enterprise Server 11 with Service Pack 2 and kernel 3\.0\.13\-0\.27
+ SUSE Linux Enterprise Server 11 with Service Pack 3 and kernel 3\.0\.76\-0\.11, 3\.0\.101\-0\.8, or 3\.0\.101\-0\.15
+ SUSE Linux Enterprise Server 11 with Service Pack 4 and kernel 3\.0\.101\-63
+ SUSE Linux Enterprise Server 12 with kernel 3\.12\.28\-4
+ SUSE Linux Enterprise Server 12 with Service Pack 1 and kernel 3\.12\.49\-11
+ SUSE Linux Enterprise Server 12 with Service Pack 2 and kernel 4\.4
+ SUSE Linux Enterprise Server 12 with Service Pack 3 and kernel 4\.4
+ CentOS 5\.1\-5\.11, 6\.1\-6\.8, 7\.0\-7\.6 \(6\.0 lacks required drivers\)
+ Debian 6\.0\.0\-6\.0\.8, 7\.0\.0\-7\.8\.0, 8\.0\.0
+ Oracle Linux 5\.10\-5\.11 with el5uek kernel suffix
+ Oracle Linux 6\.1\-6\.10 using RHEL\-compatible kernel 2\.6\.32 or UEK kernels 3\.8\.13, 4\.1\.12
+ Oracle Linux 7\.0\-7\.6 using RHEL compatible kernel 3\.10\.0 or UEK kernels 3\.8\.13, 4\.1\.12, 4\.14\.35
+ Fedora Server 19\-21
+ Amazon Linux 2

### Image Formats<a name="vmimport-image-formats"></a>

VM Import/Export supports the following image formats for importing both disks and VMs:
+ Open Virtual Appliance \(OVA\) image format, which supports importing images with multiple hard disks\.
+ Stream\-optimized ESX Virtual Machine Disk \(VMDK\) image format, which is compatible with VMware ESX and VMware vSphere virtualization products\. Note that you can only import VMDK files into Amazon EC2 that were created through the OVF export process in VMware\.
+ Fixed and Dynamic Virtual Hard Disk \(VHD/VHDX\) image formats, which are compatible with Microsoft Hyper\-V, Microsoft Azure, and Citrix Xen virtualization products\.
+ Raw format for importing disks and VMs\.

### Instance Types<a name="vmimport-instance-types"></a>

VM Import/Export supports importing Windows instances into most instance types\. For more information about using Windows and other Microsoft products within AWS, including "bring your own license" \(BYOL\), see [Amazon Web Services and Microsoft Frequently Asked Questions](https://aws.amazon.com/windows/faq/)\.

Linux instances can be imported into the following instance types:
+ General purpose: `t2.micro` \| `t2.small` \| `t2.medium` \| `m3.medium` \| `m3.large` \| `m3.xlarge` \| `m3.2xlarge`
+ Compute optimized: `c3.large` \| `c3.xlarge` \| `c3.2xlarge` \| `c3.4xlarge` \| `c3.8xlarge` \| `cc1.4xlarge` \| `cc2.8xlarge`
+ Memory optimized: `r3.large` \| `r3.xlarge` \| `r3.2xlarge` \| `r3.4xlarge` \| `r3.8xlarge` \| `cr1.8xlarge`
+ Storage optimized: `i2.xlarge` \| `i2.2xlarge` \| `i2.4xlarge` \| `i2.8xlarge` \| `hi1.4xlarge` \| `hi1.8xlarge`

### Volume Types and File Systems<a name="vmimport-volume-types"></a>

VM Import/Export supports importing Windows and Linux instances with the following file systems:

**Windows**  
MBR\-partitioned volumes and GUID Partition Table \(GPT\) partitioned volumes that are formatted using the NTFS file system\. For GPT\-partitioned volumes, only VHDX is supported as an image format\.

**Linux/Unix**  
MBR\-partitioned volumes that are formatted using the ext2, ext3, ext4, Btrfs, JFS, or XFS file system\. Btrfs subvolumes are not supported\. GUID Partition Table \(GPT\) partitioned volumes are not supported\.

## Licensing Options<a name="licensing"></a>

When you create a new VM Import task, the possible values for the `--license-type` parameter include:
+ **Auto** \(default\)

  Detects the source\-system operating system \(OS\) and applies the appropriate license to the migrated virtual machine \(VM\)\.
+ **AWS**

  Replaces the source\-system license with an AWS license, if appropriate, on the migrated VM\.
+ **BYOL**

  Retains the source\-system license, if appropriate, on the migrated VM\.

**Note**  
If you choose a license type that is incompatible with your VM, the VM Import task fails with an error message\. For more information, see the OS\-specific information below\.

Leaving the `--license-type` parameter unset is the same as choosing **Auto**\. 

### Licensing for Linux<a name="linux"></a>

Linux operating systems support only BYOL licenses\. Choosing **Auto** means that a BYOL license is used\.

Migrated Red Hat Enterprise Linux \(RHEL\) VMs must use Cloud Access \(BYOL\) licenses\. For more information, see [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) on the Red Hat website\.

Migrated SUSE Linux Enterprise Server VMs must use SUSE Public Cloud Program \(BYOS\) licenses\. For more information, see [SUSE Public Cloud Program—Bring Your Own Subscription](https://www.suse.com/docrep/documents/h4jlnjpfx3/suse_public_cloud_program_bring_your_own_subscription_faq_external.pdf)\.

### Licensing for Windows<a name="windows"></a>

Windows server operating systems support either BYOL or AWS licenses\. Windows client operating systems \(such as Windows 10\) support only BYOL licenses\. 

If you choose **Auto** \(the default\), the AWS license will be used if the VM has a server OS\. Otherwise, the BYOL license is used\. 

The following rules apply when you use your BYOL Microsoft license, either through MSDN or [Windows Software Assurance Per User](http://download.microsoft.com/download/5/c/7/5c727885-ec15-4920-818b-4d140ec6c38a/Windows_SA_per_User_at_a_Glance.pdf):
+ Your BYOL instances are priced at the prevailing Amazon EC2 Linux instance pricing, provided that you meet the following conditions: 
  + Run on a Dedicated Host \([Dedicated Hosts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)\)\.
  + Launch from VMs sourced from software binaries provided by you using AWS VM Import/Export, which are subject to the current terms and abilities of AWS VM Import/Export\.
  + Designate the instances as BYOL instances\.
  + Run the instances within your designated AWS Regions, and where AWS offers the BYOL model\.
  + Activate using Microsoft keys that you provide or which are used in your key management system\.
+ You must account for the fact that when you start an Amazon EC2 instance, it can run on any one of many servers within an Availability Zone\. This means that each time you start an Amazon EC2 instance \(including a stop/start\), it may run on a different server within an Availability Zone\. You must account for this fact in light of the limitations on license reassignment as described in Microsoft's document [Volume Licensing Product Terms](http://www.microsoftvolumelicensing.com/Downloader.aspx?documenttype=PT&lang=English&usg=AOvVaw3eaE46-Gb5hQg3r8RIv8S7), or consult your specific use rights to determine if your rights are consistent with this usage\.
+ You must be eligible to use the BYOL program for the applicable Microsoft software under your agreements with Microsoft, for example, under your MSDN user rights or under your Windows Software Assurance Per User Rights\. You are solely responsible for obtaining all required licenses and for complying with all applicable Microsoft licensing requirements, including the PUR/PT\. Further, you must have accepted Microsoft's End User License Agreement \(Microsoft EULA\), and by using the Microsoft Software under the BYOL program, you agree to the Microsoft EULA\.
+ AWS recommends that you consult with your own legal and other advisers to understand and comply with the applicable Microsoft licensing requirements\. Usage of the Services \(including usage of the **licenseType** parameter and **BYOL** flag\) in violation of your agreements with Microsoft is not authorized or permitted\.

## Limitations<a name="limitations-image"></a>
+ UEFI/EFI boot partitions are supported only for Windows boot volumes with VHDX as the image format\. Otherwise, a VM's boot volume must use Master Boot Record \(MBR\) partitions\. In either case, boot volume cannot exceed 2 TiB \(uncompressed\) due to MBR limitations\. Additional non\-bootable volumes may use GUID Partition Table \(GPT\) partitioning but cannot be bigger than 16 TiB\. If you are use VMIE APIs \(instead of AWS Server Migration Service\), you will have to construct a manifest file for disks larger than 4TiB\. For more information, see [VM Import Manifest](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/manifest.html)\.
**Note**  
When AWS detects a Windows GPT boot volume with an UEFI boot partition, it converts it on\-the\-fly to an MBR boot volume with a BIOS boot partition\. This is because EC2 does not directly support GPT boot volumes on Windows instances\.
+ An imported VM may fail to boot if the root partition is not on the same virtual hard drive as the MBR\.
+ A VM import task will fail for VMs with more than 21 volumes attached\. Additional disks can be individually imported using the `ImportSnapshot` API\.
+ Importing VMs with dual\-boot configurations is not supported\.
+  VM Import/Export does not support VMs that use Raw Device Mapping \(RDM\)\. Only VMDK disk images are supported\.
+ Imported Linux VMs must use 64\-bit images\. Migrating 32\-bit Linux images is not supported\.
+ Imported Linux VMs should use default kernels for best results\. VMs that use custom Linux kernels might not migrate successfully\.
+ When preparing Amazon EC2 Linux VMs for import, make sure that at least 250 MiB of disk space is available on the root volume for installing drivers and other software\. For Microsoft Windows VMs, configure a fixed pagefile size and ensure that at least 6 GiB of free space is available on the root volume\. If Windows is configured to use the "Automatically manage paging file size for all drives", it may create 16 GB `pagefile.sys` files on the C drive of the instance\.
+ Multiple network interfaces are not currently supported\. After import, your VM will have a single virtual network interface that uses DHCP to assign addresses\. Your instance receives a private IP address\.
+ A VM migrated into a VPC does not receive a public IP address, regardless of the auto\-assign public IP setting for the subnet\. Instead, you can allocate an Elastic IP address to your account and associate it with your instance\.
+ Internet Protocol version 6 \(IPv6\) IP addresses are not supported\.
+ VMs that are created as the result of a P2V conversion are not supported\. A P2V conversion occurs when a disk image is created by performing a Linux or Windows installation process on a physical machine and then importing a copy of that Linux or Windows installation to a VM\.
+  VM Import/Export does not install the single root I/O virtualization \(SR\-IOV\) drivers except with imports of Microsoft Windows Server 2012 R2 VMs\. These drivers are not required unless you plan to use enhanced networking, which provides higher performance \(packets per second\), lower latency, and lower jitter\. For Microsoft Windows Server 2012 R2 VMs, SR\-IOV drivers are automatically installed as a part of the import process\.
+  VM Import/Export does not support VMware SEsparse delta\-file format\.
+  VM Import/Export does not support Emergency Management Services \(EMS\)\. If EMS is enabled for a source Windows VM, we disable it in the imported image\.
+ Windows language packs that use UTF\-16 \(or non\-ASCII\) characters are not supported for import\. We recommend using the English language pack when importing Windows VMs\.
+ The base AMI used to launch an instance must exist when you attempt to export the instance\. If you have deleted the AMI, the export will fail\.

## Required Permissions for IAM Users<a name="iam-permissions-image"></a>

If you're logged in as an AWS Identity and Access Management \(IAM\) user, you'll need the following permissions in your IAM policy to use VM Import/Export:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:CreateBucket",
        "s3:DeleteBucket",
        "s3:DeleteObject",
        "s3:GetBucketLocation",
        "s3:GetObject",
        "s3:ListBucket",
        "s3:PutObject"
      ],
      "Resource": ["arn:aws:s3:::mys3bucket","arn:aws:s3:::mys3bucket/*"]
    }, 
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CancelConversionTask",
        "ec2:CancelExportTask",
        "ec2:CreateImage",
        "ec2:CreateInstanceExportTask",
        "ec2:CreateTags",
        "ec2:DeleteTags",
        "ec2:DescribeConversionTasks",
        "ec2:DescribeExportTasks",
        "ec2:DescribeExportImageTasks",
        "ec2:DescribeInstanceAttribute",
        "ec2:DescribeInstanceStatus",
        "ec2:DescribeInstances",
        "ec2:DescribeTags",
        "ec2:ExportImage",
        "ec2:ImportInstance",
        "ec2:ImportVolume",
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:TerminateInstances",
        "ec2:ImportImage",
        "ec2:ImportSnapshot",
        "ec2:DescribeImportImageTasks",
        "ec2:DescribeImportSnapshotTasks",
        "ec2:CancelImportTask"
      ],
      "Resource": "*"
    }
  ]
}
```

## Required Service Role<a name="vmimport-role"></a>

 VM Import/Export requires a role to perform certain operations on your behalf\. You must create a service role named `vmimport` with a trust relationship policy document that allows VM Import/Export to assume the role, and you must attach an IAM policy to the role\.

For more information, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-toplevel.html) in the *IAM User Guide*\.

**To create the service role**

1. Create a file named `trust-policy.json` on your computer\. Add the following policy to the file:

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
         {
            "Effect": "Allow",
            "Principal": { "Service": "vmie.amazonaws.com" },
            "Action": "sts:AssumeRole",
            "Condition": {
               "StringEquals":{
                  "sts:Externalid": "vmimport"
               }
            }
         }
      ]
   }
   ```

1. Use the [create\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html) command to create a role named `vmimport` and grant VM Import/Export access to it\. Ensure that you specify the full path to the location of the `trust-policy.json` file that you created in the previous step, and that you include the `file://` prefix as shown the following example:

   ```
   aws iam create-role --role-name vmimport --assume-role-policy-document "file://C:\import\trust-policy.json"
   ```

1. Create a file named `role-policy.json` with the following policy, where *disk\-image\-file\-bucket* is the bucket for disk images and *export\-bucket* is the bucket for exported images:

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Action":[
               "s3:GetBucketLocation",
               "s3:GetObject",
               "s3:ListBucket" 
            ],
            "Resource":[
               "arn:aws:s3:::disk-image-file-bucket",
               "arn:aws:s3:::disk-image-file-bucket/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "s3:GetBucketLocation",
               "s3:GetObject",
               "s3:ListBucket",
               "s3:PutObject",
               "s3:GetBucketAcl"
            ],
            "Resource":[
               "arn:aws:s3:::export-bucket",
               "arn:aws:s3:::export-bucket/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "ec2:ModifySnapshotAttribute",
               "ec2:CopySnapshot",
               "ec2:RegisterImage",
               "ec2:Describe*"
            ],
            "Resource":"*"
         }
      ]
   }
   ```

1. \(Optional\) If you intend to attach license configurations to an AMI, add the following License Manager permissions to the `role-policy.json` file\.

   ```
   {
     "Effect":"Allow",
     "Action":[
       "license-manager:GetLicenseConfiguration",
       "license-manager:UpdateLicenseSpecificationsForResource",
       "license-manager:ListLicenseSpecificationsForResource"
     ],
     "Resource":"*"
   }
   ```

1. Use the following [put\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command to attach the policy to the role created above\. Ensure that you specify the full path to the location of the `role-policy.json` file\.

   ```
   aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document "file://C:\import\role-policy.json"
   ```

## Required Configuration for VM Export<a name="prepare-vm-image"></a>

Use the following guidelines to configure your VM before exporting it from the virtualization environment\.

**General**
+ Install the AWS CLI on the workstation you will use to issue import commands\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.
+ Disable any antivirus or intrusion detection software on your VM\. These services can be re\-enabled after the import process is complete\.
+ Uninstall the VMware Tools from your VMware VM\.
+ Disconnect any CD\-ROM drives \(virtual or physical\)\.
+ Your source VM must have a functional DHCP client service\. Ensure that the service can start and is not disabled administratively\. All static IP addresses currently assigned to the source VM will be removed during import\. When your imported instance is launched in an Amazon VPC, it receives a primary private IP address from the IPv4 address range of the subnet\. If you don't specify a primary private IP address when you launch the instance, we select an available IP address in the subnet's IPv4 range for you\. For more information, see [VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing)\.
+ Shut down your VM before exporting it\.

**Windows**
+ Enable Remote Desktop \(RDP\) for remote access\.
+ Make sure that your host firewall \(Windows firewall or similar\), if configured, allows access to RDP\. Otherwise, you will not be able to access your instance after the import is complete\.
+ Make sure that the administrator account and all other user accounts use secure passwords\. All accounts must have passwords or the importation might fail\.
+ Install \.NET Framework 4\.5 or later on the VM\. We will install the \.NET framework on your VM as needed\.
+ You can run System Preparation \(Sysprep\) on your Windows Server VM images before or after they are imported\. If you run Sysprep before importing your VM, the importation process adds an answer file \(`unattend.xml`\) to the VM that automatically accepts the End User License Agreement \(EULA\) and sets the locale to EN\-US\. If you choose to run Sysprep after importation, we recommend that you use E2Launch \(Windows Server 2016 and later\) or EC2Config \(through Windows Server 2012 R2\) to run Sysprep\.

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

  1. Run Sysprep with the /oobe and /generalize options\. These options strip all unique system information from the Windows installation and prompt you to reset theadministrator password\.

  1. Shut down the VM and export it from your virtualization environment\.
+ Disable Autologon on your Windows VM\.
+ Open **Control Panel** > **System and Security** > **Windows Update**\. In the left pane, choose **Change settings**\. Choose the desired setting\. Be aware that if you choose **Download updates but let me choose whether to install them** \(the default value\) the update check can temporarily consume between 50% and 99% of CPU resources on the instance\. The check usually occurs several minutes after the instance starts\. Make sure that there are no pending Microsoft updates, and that the computer is not set to install software when it reboots\.
+ Apply the following hotfixes as needed:
  + [You cannot change system time if RealTimeIsUniversal registry entry is enabled in Windows](https://support.microsoft.com/en-us/help/2922223/you-cannot-change-system-time-if-realtimeisuniversal-registry-entry-is)
  + [High CPU usage during DST changeover in Windows Server 2008, Windows 7, or Windows Server 2008 R2](https://support.microsoft.com/en-us/help/2800213/high-cpu-usage-during-dst-changeover-in-windows-server-2008-windows-7)
+ Set the RealTimeIsUniversal registry key\. For more information, see [Setting the Time](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Linux**
+ Enable Secure Shell \(SSH\) for remote access\.
+ Make sure that your host firewall \(such as Linux iptables\) allows access to SSH\. Otherwise, you won't be able to access your instance after the import is complete\.
+ Make sure that you have configured a non\-root user to use public key\-based SSH to access your instance after it is imported\. The use of password\-based SSH and root login over SSH are both possible, but not recommended\. The use of public keys and a non\-root user is recommended because it is more secure\. VM Import will not configure an `ec2-user` account as part of the import process\.
+ Make sure that your Linux VM uses GRUB \(GRUB legacy\) or GRUB 2 as its bootloader\.
+ Make sure that your Linux VM uses one of the following for the root file system: EXT2, EXT3, EXT4, Btrfs, JFS, or XFS\.

## Programmatic Modifications to VMs<a name="import-modify-vm"></a>

When importing a VM, AWS modifies the file system to make the imported VM accessible to the customer\. The following actions may occur:
+ \[Linux\] Installing Citrix PV drivers either directly in OS or modify initrd/initramfs to contain them\.
+ \[Linux\] Modifying network scripts to replace static IPs with dynamic IPs\.
+ \[Linux\] Modifying `/etc/fstab`, commenting out invalid entries and replacing device names with UUIDs\. If no matching UUID can be found for a device, the `nofail` option is added to the device description\. You will need to correct the device naming and remove `nofail` after import\. As a best practice when preparing your VMs for import, we recommend that you specify your VM disk devices by UUID rather than device name\.

  Entries in `/etc/fstab` that contain non\-standard file system types \(cifs, smbfs, vboxsf, sshfs, etc\.\) will be disabled\.
+ \[Linux\] Modifying grub bootloader settings such as the default entry and timeout\.
+ \[Windows\] Modifying registry settings to make the VM bootable\.

When writing a modified file, AWS retains the original file at the same location under a new name\.