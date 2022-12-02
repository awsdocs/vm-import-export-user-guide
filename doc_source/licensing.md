# Licensing options<a name="licensing"></a>

When you create a new VM Import task, you have two options for how to specify the license type for the operating system\. You can specify a value for either the `--license-type` or the `--usage-operation` parameter\. Specifying a value for both parameters will return an error\. You can use `--usage-operation` to blend your operating system and SQL Server licenses\.

**Important**  
AWS VM Import/Export strongly recommends specifying a value for either the `--license-type` or `--usage-operation` parameter when you create a new VM Import task\. This ensures your operating system is licensed appropriately and your billing is optimized\. If you choose a license type that is incompatible with your VM, the VM Import task fails with an error message\. For more information, see the following OS\-specific information\.

**Specify license type**

You can specify the following values for the `--license-type` parameter:
+ `AWS` \(license included\)

  Replaces the source\-system license with an AWS license on the migrated VM\.
+ `BYOL`

  Retains the source\-system license on the migrated VM\.
**Note**  
Leaving the `--license-type` parameter undefined while importing a Windows Server OS is the same as choosing `AWS` and the same as choosing `BYOL` when importing a Windows client OS \(such as Windows 10\) or a Linux OS\.

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


|  Platform details  |  Usage operation \*  | 
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

\* If you are running Spot Instances, the `lineup/Operation` on your AWS Cost and Usage Report might be different from the **Usage operation** value that is listed here\. 

For example, to specify the usage operation for Windows with SQL Server Standard, run the following command:

```
aws ec2 import-image 
--usage-operation RunInstances:0006
--disk-containers Format=OVA,Url=S3://bucket_name/sql_std_image.ova
```

For more information about billing codes, see [AMI billing information fields](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/billing-info-fields.html)\.

## Licensing for Linux<a name="linux"></a>

Linux operating systems support only the `BYOL` license type for a VM import task\.

Migrated Red Hat Enterprise Linux \(RHEL\) VMs must use Cloud Access \(BYOS\) licenses\. For more information, see [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) on the Red Hat website\.

Migrated SUSE Linux Enterprise Server VMs must use SUSE Public Cloud Program \(BYOS\) licenses\. For more information, see [SUSE Public Cloud Program—Bring Your Own Subscription](https://www.suse.com/media/flyer/suse_subscription_portability_in_the_public_cloud_flyer.pdf)\.

## Licensing for Windows<a name="windows"></a>

Windows Server operating systems support either the `BYOL` or `AWS` license type\. Windows client operating systems \(such as Windows 10\) support only BYOL licenses\.

By default, an AWS license is used when you create a VM import task if the VM has a Windows Server OS\. Otherwise, a BYOL license is used\.

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

  For more information, see *[Generating Windows Server and SQL Server on Amazon EC2 estimates](https://docs.aws.amazon.com/pricing-calculator/latest/userguide/windows-workload-estimates.html) in the AWS Pricing Calculator User Guide*\.