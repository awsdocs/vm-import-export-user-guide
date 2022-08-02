# System requirements<a name="prerequisites"></a>

Before you begin, you must be aware of the operating systems and image formats that VM Import/Export supports, and understand the limitations on importing instances and volumes\.

## Image formats<a name="vmimport-image-formats"></a>

VM Import/Export supports the following image formats for importing both disks and VMs:
+ Open Virtual Appliance \(OVA\) image format, which supports importing images with multiple hard disks\.
+ Stream\-optimized ESX Virtual Machine Disk \(VMDK\) image format, which is compatible with VMware ESX and VMware vSphere virtualization products\.
+ Fixed and Dynamic Virtual Hard Disk \(VHD/VHDX\) image formats, which are compatible with Microsoft Hyper\-V, Microsoft Azure, and Citrix Xen virtualization products\.
+ Raw format for importing disks and VMs\.

## Operating systems<a name="vmimport-operating-systems"></a>

The following operating systems \(OS\) can be imported to and exported from Amazon EC2\.

### Linux/Unix OS \(64\-bit only\)<a name="vmimport-operating-systems-linux"></a><a name="linux-operating-systems"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vm-import/latest/userguide/prerequisites.html)

### Windows OS<a name="vmimport-operating-systems-windows"></a><a name="windows-operating-systems"></a>


| Operating system | Edition | Bit version | Available with non\-default Regions | 
| --- | --- | --- | --- | 
| Microsoft Windows Server 2003 \(Service Pack 1 or later\) | Standard, Datacenter, Enterprise | 32, 64 | No | 
| Microsoft Windows Server 2003 R2 | Standard, Datacenter, Enterprise | 32, 64 | No | 
| Microsoft Windows Server 2008 | Standard, Datacenter, Enterprise | 32, 64 | No | 
| Microsoft Windows Server 2008 R2 | Standard, Web Server, Datacenter, Enterprise | 64 | Yes 5 | 
| Microsoft Windows Server 2012 | Standard, Datacenter | 64 | Yes 5 | 
| Microsoft Windows Server 2012 R2 | Standard, Datacenter | 64 | Yes 5 | 
| Microsoft Windows Server 2016 | Standard, Datacenter 3 | 64 | Yes 5 | 
| Microsoft Windows Server 1709 | Standard, Datacenter | 64 | Yes 5 | 
| Microsoft Windows Server 1803 | Standard, Datacenter | 64 | Yes 5 | 
| Microsoft Windows Server 2019 | Standard, Datacenter | 64 | Yes 5 | 
| Microsoft Windows 7 1 | Home, Professional, Enterprise, Ultimate | 32, 64 4 | Yes 5 | 
| Microsoft Windows 8 1 | Home, Professional, Enterprise | 32, 64 4 | Yes 5 | 
| Microsoft Windows 8\.1 1 | Professional, Enterprise | 64 | Yes 5 | 
| Microsoft Windows 10 1 | Home, Professional, Enterprise, Education | 64 | Yes 5 | 
| Microsoft Windows 11 1,2 | Home, Professional, Enterprise, Education | 64 | Yes 5,6 | 

1 The operating system must have its language set as `US English` during import\.

2 Windows 11 requires the Unified Extensible Firmware Interface \(UEFI\) boot mode to function\. To help ensure a successful import of your VM, we recommend that you specify the optional `--boot-mode` parameter as `uefi`\. For more information, see [Boot modes](#vmimport-boot-modes)\.

3 Nano Server installations are not supported\.

4 Only the 64\-bit version of the OS is supported when launching instances within non\-default AWS Regions\. For more information, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Windows Instances*\.

5 You must first enable the Region before you can use the operating system there\. For more information, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Windows Instances* and [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *Amazon Web Services General Reference Guide*\.

6 Windows 11 is not supported in the Asia Pacific \(Jakarta\) Region\.

## Boot modes<a name="vmimport-boot-modes"></a>

When a computer boots, the first software that it runs is responsible for initializing the platform and providing an interface for the operating system to perform platform\-specific operations\. VM Import/Export supports two variants of the boot mode: Unified Extensible Firmware Interface \(UEFI\) and Legacy BIOS\. You can choose whether to specify the optional `--boot-mode` parameter as `legacy-bios` or `uefi` when importing your VM\.

Refer to the [Boot Modes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ami-boot.html) section of the *Amazon Elastic Compute Cloud User Guide* for more information about specifying a boot mode, and UEFI variables\.

## Volume types and file systems<a name="vmimport-volume-types"></a>

VM Import/Export supports importing Windows and Linux VMs with the following file systems:

**Windows**  
GUID Partition Table \(GPT\) and Master Boot Record \(MBR\) partitioned volumes that are formatted using the NTFS file system are supported\. If no boot parameter is specified, and the VM is compatible in both boot modes, the GPT volumes will be converted to MBR partitioned volumes\.

**Note**  
VM Import/Export will automatically detect the boot modes your Windows VM is compatible with\. If the Windows VM is only compatible in a single boot mode, you don't need to specify a specific `--boot-mode` parameter\.  
If your Windows VM is compatible with both boot modes, and the following criteria is met for the imported disk, VM Import/Export will select Legacy BIOS by default\. You can specify `uefi` for the `--boot-mode` parameter to override this behavior\.  
The disk is smaller than 2 terabytes
The disk does not contain more than 4 primary partitions
The disk is not a Windows dynamic disk
The file format is VHDX

**Linux/Unix**  
MBR partitioned volumes and GUID Partition Table \(GPT\) partitioned volumes that are formatted using the ext2, ext3, ext4, Btrfs, JFS, or XFS file system are supported\.

**Important**  
Btrfs subvolumes are not supported\.