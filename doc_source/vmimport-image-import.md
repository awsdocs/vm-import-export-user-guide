# Importing a VM as an Image Using VM Import/Export<a name="vmimport-image-import"></a>

You can use VM Import/Export to import virtual machine \(VM\) images from your virtualization environment to Amazon EC2 as Amazon Machine Images \(AMI\), which you can use to launch instances\. Subsequently, you can export the VM images from an instance back to your virtualization environment\. This enables you to leverage your investments in the VMs that you have built to meet your IT security, configuration management, and compliance requirements by bringing them into Amazon EC2\.

**Important**  
For most VM import needs, we recommend that you use the AWS Server Migration Service\. AWS SMS automates the import process \(reducing the workload of migrating large VM infrastructures\), adds support for incremental updates of changing VMs, and converts your imported VMs into ready\-to\-use Amazon machine images \(AMIs\)\. To get started with AWS SMS, see [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)\.

**Topics**
+ [Export Your VM from its Virtualization Environment](#export-vm-image)
+ [Import Your VM as an Image](#import-vm-image)
+ [Monitor an Import Image Task](#check-import-task-status)
+ [Cancel an Import Image Task](#cancel-upload)
+ [Next Steps](#next-steps)

## Export Your VM from its Virtualization Environment<a name="export-vm-image"></a>

After you have prepared your VM for export, you can export it from your virtualization environment\. When importing a VM as an image, you can import disks in the following formats: Open Virtualization Archive \(OVA\), Virtual Machine Disk \(VMDK\), Virtual Hard Disk \(VHD/VHDX\), and raw\. With some virtualization environments, you would export to Open Virtualization Format \(OVF\), which typically includes one or more VMDK, VHD, or VHDX files, and then package the files into an OVA file\.

For more information, see the documentation for your virtualization environment\. For example:
+ **VMware** — Search for "Export an OVF Template" on the [VMware Docs](https://docs.vmware.com/) site\. Follow the instructions for creating an OVA\.
+ **Citrix** — [About VM Import and Export](https://docs.citrix.com/en-us/xencenter/7-1/vms-exportimport-about.html) on the Citrix website
+ **Microsoft Hyper\-V** — [Overview of exporting and importing a virtual machine](https://technet.microsoft.com/en-us/library/hh831535.aspx) on the Microsoft website
+ **Microsoft Azure** — [Download a Windows VHD from Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/download-vhd) or [Download a Linux VHD from Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/download-vhd) on the Microsoft website\. From the Azure Portal, choose the VM to migrate, and then choose **Disks**\. Select each disk \(either OS or data\) and choose **Create Snapshot**\. On the completed snapshot resource, choose **Export**\. This creates a URL that you can use to download the virtual image\.

## Import Your VM as an Image<a name="import-vm-image"></a>

After exporting your VM from your virtualization environment, you can import it to Amazon EC2\. The import process is the same regardless of the origin of the VM\.

**Topics**
+ [Prerequisites](#import-image-prereqs)
+ [Upload the Image to Amazon S3](#upload-image)
+ [Import the VM](#import-vm)

### Prerequisites<a name="import-image-prereqs"></a>
+ Create an Amazon S3 bucket for storing the exported images or choose an existing bucket\. The bucket must be in the Region where you want to import your VMs\. For more information about S3 buckets, see the [Amazon Simple Storage Service Console User Guide](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/)\.
+ Create an IAM role named `vmimport`\. For more information, see [Required Service Role](vmie_prereqs.md#vmimport-role)\.
+ If you have not already installed the AWS CLI on the computer you'll use to run the import commands, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

### Upload the Image to Amazon S3<a name="upload-image"></a>

Upload your VM image file to your Amazon S3 bucket using the upload tool of your choice\. For information about uploading objects through the Amazon S3 console, see [Uploading Objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html)\.

### Import the VM<a name="import-vm"></a>

After you upload your VM image file to Amazon S3, you can use the AWS CLI to import the image\. These tools accept either the Amazon S3 bucket and path to the file or a URL for a public Amazon S3 file\. Private Amazon S3 files require a [presigned URL]( https://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html)\.

The following examples use the AWS CLI command [import\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) to create import tasks\.

**Example 1: Import an OVA**

Use the following command to import an image with a single disk\.

```
aws ec2 import-image --description "My server VM" --disk-containers "file://C:\import\containers.json"
```

The following is an example `containers.json` file\.

```
[
  {
    "Description": "My Server OVA",
    "Format": "ova",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "vms/my-server-vm.ova"
    }
}]
```

**Example 2: Import Multiple Disks**

Use the following command to import an image with multiple disks\.

```
$ C:\> aws ec2 import-image --description "My server disks" --disk-containers "file://C:\import\containers.json"
```

The following is an example `containers.json` file\.

```
[
  {
    "Description": "First disk",
    "Format": "vmdk",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "disks/my-server-vm-disk1.vmdk"
    }
  },          
  {
    "Description": "Second disk",
    "Format": "vmdk",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "disks/my-server-vm-disk2.vmdk"
    }
  }
]
```

**Example 3: Import Disk with Encrypted Option Enabled**

Use the following command to import an image with an encrypted root volume\.

```
aws ec2 import-image --description "My server disks" --encrypted --kms-key-id 0ea3fef3-80a7-4778-9d8c-1c0c6EXAMPLE --disk-containers "file://C:\import\containers.json"
```

The CMK provided for encryption must not be disabled during the entire import process\. For more information, see [Amazon EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) in the *Amazon EC2 User Guide*\.

## Monitor an Import Image Task<a name="check-import-task-status"></a>

Use the [describe\-import\-image\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-image-tasks.html) command to return the status of an import task\.

```
aws ec2 describe-import-image-tasks --import-task-ids import-ami-1234567890abcdef0
```

Status values include the following:
+ `active` — The import task is in progress\.
+ `deleting` — The import task is being canceled\.
+ `deleted` — The import task is canceled\.
+ `updating` — Import status is updating\.
+ `validating` — The imported image is being validated\.
+ `validated` — The imported image was validated\.
+ `converting` — The imported image is being converted into an AMI\.
+ `completed` — The import task is completed and the AMI is ready to use\.

## Cancel an Import Image Task<a name="cancel-upload"></a>

If you need to cancel an active import task, use the [cancel\-import\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-import-task.html) command\.

```
aws ec2 cancel-import-task --import-task-id import-ami-1234567890abcdef0
```

## Next Steps<a name="next-steps"></a>

For some operating systems, the device drivers for enhanced networking and NVMe block devices that are required by [Nitro\-based instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) are not installed automatically during import\. To install these drivers manually, use the directions in the following documentation\. Next, create a new AMI from the customized instance\.

**Windows**
+ \(Recommended\) [Installing the Latest Version of EC2Config](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html) or [Installing the Latest Version of EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-download.html)
+ [Enabling Enhanced Networking on Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)
+ [AWS NVMe Drivers for Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-nvme-drivers.html)

**Linux**
+ [Enabling Enhanced Networking on Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking-ena.html)
+ [Install or Upgrade the NVMe Driver](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/nvme-ebs-volumes.html#install-nvme-driver)

After you have an AMI with the required drivers, you can launch it as an instance or copy it to another Region\. For more information, see the following documentation\.

**Windows**
+ [Launching an Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/launching-instance.html)
+ [Copying an AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/CopyingAMIs.html)

**Linux**
+ [Launching an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html)
+ [Copying an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html)