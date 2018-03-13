# Exporting an Instance as a VM Using VM Import/Export<a name="vmexport"></a>

If you have previously used VM Import to import a virtual machine into Amazon EC2, you can export that EC2 instance to Citrix Xen, Microsoft Hyper\-V, or VMware vSphere\. Exporting an instance that you previously imported is useful when you want to deploy a copy of your EC2 instance in your on\-site virtualization environment\.

**Important**  
When you export an instance, you are charged the standard Amazon S3 rates for the bucket where the exported VM is stored\. In addition, there might be a small charge for the temporary use of an Amazon EBS snapshot\. For more information about Amazon S3 pricing, see [Amazon Simple Storage Service Pricing](https://aws.amazon.com/s3/pricing/)\.

If you're using VMware vSphere, you can alternatively use the AWS Connector for vCenter to export a VM from Amazon EC2\. For more information, see [Exporting a Migrated Amazon EC2 Instance](http://docs.aws.amazon.com/amp/latest/userguide/migrate-vms.html#export-instance) in the *AWS Management Portal for vCenter User Guide*\.


+ [Prerequisites](#vmexport-prerequisites)
+ [Limitations](#vmexport-limits)
+ [Export an Instance](#export-instance)
+ [Monitor Instance Export](#vmexport-monitor)
+ [Cancel or Stop Instance Export](#vmexport-cancel)

## Prerequisites<a name="vmexport-prerequisites"></a>

To export a VM from Amazon EC2, install the AWS CLI on the instance\. For more information, see the [AWS Command Line Interface User Guide](http://docs.aws.amazon.com/cli/latest/userguide/)\.

You can create an Amazon S3 bucket for storing the exported instances and grant VM Import/Export permission to access the bucket\. If you already have a bucket and want to use it, you can use it instead of creating a bucket\.

**\(Optional\) To create an S3 bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create Bucket**\. 

1. In the **Create a Bucket** dialog box, do the following:

   1. For **Bucket Name**, type a name for your bucket\. This name must be unique across all existing bucket names in Amazon S3\. In some regions, there might be additional restrictions on bucket names\. For more information, see [Bucket Restrictions and Limitations](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

   1. For **Region**, select a region\.

   1. Choose **Create**\. When the bucket is created, the details pane opens with the **Permissions** section expanded\.

   1. Choose **Add More Permissions**\. 

   1. For **Grantee**, type the appropriate region\-specific canonical account ID:

      + **China \(Beijing\)**: 834bafd86b15b6ca71074df0fd1f93d234b9d5e848a2cb31f880c149003ce36f

      + **AWS GovCloud \(US\)**: af913ca13efe7a94b88392711f6cfc8aa07c9d1454d4f190a624b126733a5602

      + **All other regions**: c4d8eabf8db69dbe46bfe0e517100c554f01200b104d59cd408e777ba442a322

   1. Select **Upload/Delete** and **View Permissions**\.

   1. Choose **Save**\.

For more information, see [Editing Bucket Permissions](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/EditingBucketPermissions.html) in the *Amazon Simple Storage Service Console User Guide*\.

## Limitations<a name="vmexport-limits"></a>

Exporting instances and volumes is subject to the following limitations:

+ You can't export an instance from Amazon EC2 unless you previously imported it into Amazon EC2 from another virtualization environment\.

+ You must export your instances and volumes to one of the following image formats that your virtualization environment supports:

  + Open Virtual Appliance \(OVA\), which is compatible with VMware vSphere versions 4, 5, and 6\.

  + Virtual Hard Disk \(VHD\), which is compatible with Citrix Xen and Microsoft Hyper\-V virtualization products\.

  + Stream\-optimized ESX Virtual Machine Disk \(VMDK\), which is compatible with VMware ESX and VMware vSphere versions 4, 5, and 6\.

+ You can't export Amazon EBS data volumes\.

+ You can't export an instance that has more than one virtual disk\.

+ You can't export an instance that has more than one network interface\.

+ You can't export an instance from Amazon EC2 if you've shared it from another AWS account\.

+ You can't have more than five export tasks per region in progress at the same time\.

+ VMs with volumes larger than 1 TiB are not supported\.

## Export an Instance<a name="export-instance"></a>

To export your instance, use the [create\-instance\-export\-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) command\. You must provide the information required to properly export the instance to the selected virtualization format\. The exported file is saved in the S3 bucket that you specify\.

```
aws ec2 create-instance-export-task --instance-id id --target-environment target_environment \
--export-to-s3-task DiskImageFormat=disk_image_format,ContainerFormat=ova,S3Bucket=bucket,S3Prefix=prefix
```Options

*id*  
The ID of the instance\.

*target\_environment*  
The target environment \(`vmware` | `citrix` | `microsoft`\)\.

*disk\_image\_format*  
The disk image format \(`VMDK` | `VHD`\)\.

*bucket*  
The name of the S3 bucket\.

*prefix*  
The image is written to an object in the S3 bucket using the following S3 key: *prefix*/export\-i\-*xxxxxxxx*\.*format* \(for example, my\-exports/export\-i\-12345678\.ova\)\.

## Monitor Instance Export<a name="vmexport-monitor"></a>

To monitor the export of your instance, use the following [describe\-export\-tasks](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-export-tasks.html) command:

```
aws ec2 describe-export-tasks --export-task-ids export-i-12345678
```

## Cancel or Stop Instance Export<a name="vmexport-cancel"></a>

You can use the following [cancel\-export\-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-export-task.html) command to cancel or stop the export of an instance up to the point of completion\.

```
aws ec2 cancel-export-task --export-task-id export-i-12345678
```

The cancel\-export\-task command removes all artifacts of the export, including any partially created Amazon S3 objects\. If the export task is complete or is in the process of transferring the final disk image, the command fails and returns an error\.