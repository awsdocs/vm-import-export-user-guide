# VM Import/Export Requirements<a name="vmie_prereqs"></a>

Before attempting to import a VM, take action as needed to meet the following requirements\. You may also need to prepare your AWS environment by creating a service account with appropriate permissions, and you must prepare your locally hosted VM so that is accessible once it is imported into AWS\.

**Contents**
+ [System requirements](#prerequisites)
  + [Image formats](#vmimport-image-formats)
  + [Operating systems](#vmimport-operating-systems)
  + [Volume types and file systems](#vmimport-volume-types)
+ [Licensing options](#licensing)
  + [Licensing for Linux](#linux)
  + [Licensing for Windows](#windows)
+ [Limitations](#limitations-image)
+ [Required permissions for IAM users](#iam-permissions-image)
+ [Required service role](#vmimport-role)
+ [Required configuration for VM export](#prepare-vm-image)
+ [Programmatic modifications to VMs](#import-modify-vm)

## System requirements<a name="prerequisites"></a>

Before you begin, you must be aware of the operating systems and image formats that VM Import/Export supports, and understand the limitations on importing instances and volumes\.

### Image formats<a name="vmimport-image-formats"></a>

VM Import/Export supports the following image formats for importing both disks and VMs:
+ Open Virtual Appliance \(OVA\) image format, which supports importing images with multiple hard disks\.
+ Stream\-optimized ESX Virtual Machine Disk \(VMDK\) image format, which is compatible with VMware ESX and VMware vSphere virtualization products\.
+ Fixed and Dynamic Virtual Hard Disk \(VHD/VHDX\) image formats, which are compatible with Microsoft Hyper\-V, Microsoft Azure, and Citrix Xen virtualization products\.
+ Raw format for importing disks and VMs\.

### Operating systems<a name="vmimport-operating-systems"></a>

The following operating systems can be imported to and exported from Amazon EC2\. For more information about whether a Region is enabled by default, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Linux Instances*\.<a name="windows-operating-systems"></a>

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
+ Microsoft Windows 10 \(Home, Professional, Enterprise, Education\) \(US English\)<a name="linux-operating-systems"></a>

**Linux/Unix \(64\-bit only\)**
+ Amazon Linux 2
+ CentOS 5\.1\-5\.11, 6\.1\-6\.8, 7\.0\-7\.9, 8\.0\-8\.2
+ Debian 6\.0\.0\-6\.0\.8, 7\.0\.0\-7\.8\.0, 8\.0\.0
+ Fedora Server 19\-21
+ Oracle Linux 5\.10\-5\.11 with el5uek kernel suffix
+ Oracle Linux 6\.1\-6\.10 using RHEL\-compatible kernel 2\.6\.32 or UEK kernels 3\.8\.13, 4\.1\.12
+ Oracle Linux 7\.0\-7\.6 using RHEL compatible kernel 3\.10\.0 or UEK kernels 3\.8\.13, 4\.1\.12, 4\.14\.35, 4\.15, 5\.4\.17
+ Red Hat Enterprise Linux \(RHEL\) 5\.1\-5\.11, 6\.1\-6\.9, 7\.0\-7\.9, 8\.0\-8\.2
+ SUSE Linux Enterprise Server 11 with Service Pack 1 and kernel 2\.6\.32\.12\-0\.7
+ SUSE Linux Enterprise Server 11 with Service Pack 2 and kernel 3\.0\.13\-0\.27
+ SUSE Linux Enterprise Server 11 with Service Pack 3 and kernel 3\.0\.76\-0\.11, 3\.0\.101\-0\.8, or 3\.0\.101\-0\.15
+ SUSE Linux Enterprise Server 11 with Service Pack 4 and kernel 3\.0\.101\-63
+ SUSE Linux Enterprise Server 12 with kernel 3\.12\.28\-4
+ SUSE Linux Enterprise Server 12 with Service Pack 1 and kernel 3\.12\.49\-11
+ SUSE Linux Enterprise Server 12 with Service Pack 2 and kernel 4\.4
+ SUSE Linux Enterprise Server 12 with Service Pack 3 and kernel 4\.4
+ Ubuntu 12\.04, 12\.10, 13\.04, 13\.10, 14\.04, 14\.10, 15\.04, 16\.04, 16\.10, 17\.04, 18\.04, and 20\.04, with a supported kernel\. For example, Ubuntu 18\.04 requires kernel 4\.15\.

### Volume types and file systems<a name="vmimport-volume-types"></a>

VM Import/Export supports importing Windows and Linux instances with the following file systems:

**Windows**  
MBR\-partitioned volumes and GUID Partition Table \(GPT\) partitioned volumes that are formatted using the NTFS file system\. For GPT\-partitioned volumes, only VHDX is supported as an image format\.

**Linux/Unix**  
MBR\-partitioned volumes that are formatted using the ext2, ext3, ext4, Btrfs, JFS, or XFS file system\. Btrfs subvolumes are not supported\. GUID Partition Table \(GPT\) partitioned volumes are not supported\.

## Licensing options<a name="licensing"></a>

When you create a new VM Import task, you have two options\. You can specify values for the `--license-type` or the `--usage-operation` parameters\. You can specify values for only one of these parameters\. Specifying values for both parameters will return an error\. You can use `--usage-operation` to blend your OS and SQL Server licenses\.

**Specify license type**

You can specify the following values for the `--license-type` parameter:
+ **Auto** \(default\)

  Detects the source\-system operating system \(OS\) and applies the appropriate license to the migrated virtual machine \(VM\)\.
+ **AWS** \(license included\)

  Replaces the source\-system license with an AWS license, if appropriate, on the migrated VM\.
+ **BYOL**

  Retains the source\-system license, if appropriate, on the migrated VM\.

**Note**  
If you choose a license type that is incompatible with your VM, the VM Import task fails with an error message\. For more information, see the OS\-specific information below\.

Leaving the `--license-type` parameter unset is the same as choosing **Auto**\. 

For example, to specify the license type as an AWS license, run the following command:

```
aws ec2 import-image 
--license-type aws
--disk-containers Format=OVA,Url=S3://bucket_name/sql_std_image.ova
```

**Specify usage operation**

**Important**  
AWS stamps the software edition with the information that you provide\. You are responsible for entering the correct software edition information for any licenses that you bring to AWS\.

You can specify the following values for the `--usage-operation` parameter:


|  Platform details  |  Usage operation\*\*  | 
| --- | --- | 
|  Windows Server License Included without SQL Server  |  RunInstances:0002  | 
|  Windows Server License Included with SQL Server \(any edition\) BYOL  |  RunInstances:0002  | 
|  Windows Server License Included with SQL Server Standard License Included  |  RunInstances:0006  | 
| Windows Server License Included with SQL Server Enterprise License Included | RunInstances:0102 | 
| Windows Server License Included with SQL Server Web License Included | RunInstances:0202 | 
| Windows Server BYOL without SQL Server | RunInstances:0800 | 
| Windows Server BYOL with SQL \(any edition\) BYOL | RunInstances:0800 | 
| Linux/UNIX without SQL Server | RunInstances | 
| Linux/UNIX with SQL Server \(any edition\) BYOL | RunInstances | 
| Linux/UNIX with SQL Server Enterprise License Included | RunInstances:0100 | 
| Linux/UNIX with SQL Server Standard License Included | RunInstances:0004 | 
| Linux/UNIX with SQL Server Web License Included | RunInstances:0200 | 

\*\* If you are running Spot Instances, the `lineup/Operation` on your AWS Cost and Usage Report might be different from the **Usage operation** value that is listed here\. 

For example, to specify the usage operation for Windows with SQL Server Standard, run the following command:

```
aws ec2 import-image 
--usage-operation RunInstances:0006
--disk-containers Format=OVA,Url=S3://bucket_name/sql_std_image.ova
```

For more information about billing codes, see [AMI billing information fields](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/billing-info-fields.html)\.

### Licensing for Linux<a name="linux"></a>

Linux operating systems support only BYOL licenses\. Choosing **Auto** means that a BYOL license is used\.

Migrated Red Hat Enterprise Linux \(RHEL\) VMs must use Cloud Access \(BYOL\) licenses\. For more information, see [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) on the Red Hat website\.

Migrated SUSE Linux Enterprise Server VMs must use SUSE Public Cloud Program \(BYOS\) licenses\. For more information, see [SUSE Public Cloud Program—Bring Your Own Subscription](https://www.suse.com/docrep/documents/h4jlnjpfx3/suse_public_cloud_program_bring_your_own_subscription_faq_external.pdf)\.

### Licensing for Windows<a name="windows"></a>

Windows server operating systems support either BYOL or AWS licenses\. Windows client operating systems \(such as Windows 10\) support only BYOL licenses\. 

If you choose **Auto** \(the default\), the AWS license is used if the VM has a server OS\. Otherwise, the BYOL license is used\. 

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
+ An imported VM may fail to boot if the root partition is not on the same virtual hard drive as the MBR\.
+ A VM import task fails for VMs with more than 21 volumes attached\. Additional disks can be individually imported using the `ImportSnapshot` API\.
+ Importing VMs with dual\-boot configurations is not supported\.
+ VM Import/Export does not support VMs that use Raw Device Mapping \(RDM\)\. Only VMDK disk images are supported\.
+ Imported Linux VMs must use 64\-bit images\. Migrating 32\-bit Linux images is not supported\.
+ Imported Linux VMs should use default kernels for best results\. VMs that use custom Linux kernels might not migrate successfully\.
+ When preparing Amazon EC2 Linux VMs for import, make sure that there is sufficient disk space available on the root volume for installing drivers and other software\. For Microsoft Windows VMs, configure a fixed pagefile size and ensure that there is at least 6 GiB of free space available on the root volume\. If Windows is configured to use the "Automatically manage paging file size for all drives", it might create 16 GB `pagefile.sys` files on the C drive of the instance\.
+ Multiple network interfaces are not currently supported\. After import, your VM has a single virtual network interface that uses DHCP to assign addresses\. Your instance receives a private IP address\.
+ A VM migrated into a VPC does not receive a public IP address, regardless of the auto\-assign public IP setting for the subnet\. Instead, you can allocate an Elastic IP address to your account and associate it with your instance\.
+ VM Import/Export assigns only IPv4 addresses to your instances\. You can add IPv6 addresses\.
+ Disk images must be less than 16 TiB\. For disk images that are larger than 8 TiB, you must use a [manifest file](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/manifest.html)\.
+ If you import a VM that's compatible with UEFI, you can't specify encryption, you can't specify a license configuration, and you can't specify an EBS snapshot unless you also specify a platform\. On Linux, you must have a fallback EFI binary, BOOTX64\.EFI, located on the EFI System Partition\. On Windows, we convert GPT boot volumes to MBR if the following are true: the image format is VHDX, the uncompressed size is 2 TiB or smaller, there are no more than three primary partitions, and the volume is not a dynamic disk\.
+ VMs that are created as the result of a P2V conversion are not supported\. A P2V conversion occurs when a disk image is created by performing a Linux or Windows installation process on a physical machine and then importing a copy of that Linux or Windows installation to a VM\.
+ VM Import/Export does not install the single root I/O virtualization \(SR\-IOV\) drivers except with imports of Microsoft Windows Server 2012 R2 VMs\. These drivers are not required unless you plan to use enhanced networking, which provides higher performance \(packets per second\), lower latency, and lower jitter\. For Microsoft Windows Server 2012 R2 VMs, SR\-IOV drivers are automatically installed as a part of the import process\.
+ VM Import/Export does not support VMware SEsparse delta\-file format\.
+ VM Import/Export does not support Emergency Management Services \(EMS\)\. If EMS is enabled for a source Windows VM, we disable it in the imported image\.
+ Windows language packs that use UTF\-16 \(or non\-ASCII\) characters are not supported for import\. We recommend using the English language pack when importing Windows VMs\.
+ The base AMI used to launch an instance must exist when you attempt to export the instance\. If you have deleted the AMI, the export fails\.

## Required permissions for IAM users<a name="iam-permissions-image"></a>

If you're logged in as an AWS Identity and Access Management \(IAM\) user, you'll need the following permissions in your IAM policy to use VM Import/Export:

**Note**  
Some actions require the use of an Amazon S3 bucket\. This example policy does not grant permission to create Amazon S3 buckets\. The IAM user will need to specify an existing bucket, or have permissions to create a new bucket \(` s3:CreateBucket`\)\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetBucketLocation",
        "s3:GetObject",
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
        "ec2:DescribeConversionTasks",
        "ec2:DescribeExportTasks",
        "ec2:DescribeExportImageTasks",
        "ec2:DescribeImages",
        "ec2:DescribeInstanceStatus",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
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

## Required service role<a name="vmimport-role"></a>

VM Import/Export requires a role to perform certain operations on your behalf\. You must create a service role named `vmimport` with a trust relationship policy document that allows VM Import/Export to assume the role, and you must attach an IAM policy to the role\. For more information, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-toplevel.html) in the *IAM User Guide*\.

**Prerequisite**  
You must enable AWS Security Token Service \(AWS STS\) in any Region where you plan to use VM Import/Export\. For more information, see [Activating and deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate)\.

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
            "Effect": "Allow",
            "Action": [
               "s3:GetBucketLocation",
               "s3:GetObject",
               "s3:ListBucket" 
            ],
            "Resource": [
               "arn:aws:s3:::disk-image-file-bucket",
               "arn:aws:s3:::disk-image-file-bucket/*"
            ]
         },
         {
            "Effect": "Allow",
            "Action": [
               "s3:GetBucketLocation",
               "s3:GetObject",
               "s3:ListBucket",
               "s3:PutObject",
               "s3:GetBucketAcl"
            ],
            "Resource": [
               "arn:aws:s3:::export-bucket",
               "arn:aws:s3:::export-bucket/*"
            ]
         },
         {
            "Effect": "Allow",
            "Action": [
               "ec2:ModifySnapshotAttribute",
               "ec2:CopySnapshot",
               "ec2:RegisterImage",
               "ec2:Describe*"
            ],
            "Resource": "*"
         }
      ]
   }
   ```

1. \(Optional\) To import resources encrypted using an AWS KMS key from AWS Key Management Service, add the following permissions to the `role-policy.json` file\.

   ```
   {
     "Effect": "Allow",
     "Action": [
       "kms:CreateGrant",
       "kms:Decrypt",
       "kms:DescribeKey",
       "kms:Encrypt",
       "kms:GenerateDataKey*",
       "kms:ReEncrypt*"
     ],
     "Resource": "*"
   }
   ```

   If you use a KMS key other than the default provided by Amazon EBS, you must grant VM Import/Export permission to the KMS key if you enable Amazon EBS encryption by default or enable encryption on an import operation\. You can specify the Amazon Resource Name \(ARN\) of the KMS key as the resource instead of \*\.

1. \(Optional\) To attach license configurations to an AMI, add the following License Manager permissions to the `role-policy.json` file\.

   ```
   {
     "Effect": "Allow",
     "Action": [
       "license-manager:GetLicenseConfiguration",
       "license-manager:UpdateLicenseSpecificationsForResource",
       "license-manager:ListLicenseSpecificationsForResource"
     ],
     "Resource": "*"
   }
   ```

1. Use the following [put\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command to attach the policy to the role created above\. Ensure that you specify the full path to the location of the `role-policy.json` file\.

   ```
   aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document "file://C:\import\role-policy.json"
   ```

## Required configuration for VM export<a name="prepare-vm-image"></a>

Use the following guidelines to configure your VM before exporting it from the virtualization environment\.

**General**
+ Install the AWS CLI on the workstation you will use to issue import commands\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.
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

## Programmatic modifications to VMs<a name="import-modify-vm"></a>

When importing a VM using the `ImportImage` API, AWS modifies the file system to make the imported VM accessible to the customer\. The following actions may occur:
+ \[Linux\] Installing Citrix PV drivers either directly in OS or modify initrd/initramfs to contain them\.
+ \[Linux\] Modifying network scripts to replace static IPs with dynamic IPs\.
+ \[Linux\] Modifying `/etc/fstab`, commenting out invalid entries and replacing device names with UUIDs\. If no matching UUID can be found for a device, the `nofail` option is added to the device description\. You must correct the device naming and remove `nofail` after import\. As a best practice when preparing your VMs for import, we recommend that you specify your VM disk devices by UUID rather than device name\.

  Entries in `/etc/fstab` that contain non\-standard file system types \(cifs, smbfs, vboxsf, sshfs, etc\.\) are disabled\.
+ \[Linux\] Modifying grub bootloader settings such as the default entry and timeout\.
+ \[Windows\] Modifying registry settings to make the VM bootable\.
+ \[Both\] For parity with images provided by AWS, the AWS Systems Manager client is installed on the VM\.

When writing a modified file, AWS retains the original file at the same location under a new name\.