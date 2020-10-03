# Exporting an Instance as a VM Using VM Import/Export<a name="vmexport"></a>

Exporting as a VM is useful when you want to deploy a copy of an Amazon EC2 instance in your on\-site virtualization environment\. You can export most EC2 instances to Citrix Xen, Microsoft Hyper\-V, or VMware vSphere\.

**Note**  
If you're using VMware vSphere, you can alternatively use the AWS Connector for vCenter to export a VM from Amazon EC2\. For more information, see [Exporting a Migrated Amazon EC2 Instance](https://docs.aws.amazon.com/amp/latest/userguide/migrate-vms.html#export-instance) in the *AWS Management Portal for vCenter User Guide*\.

When you export an instance, you are charged the standard Amazon S3 rates for the bucket where the exported VM is stored\. In addition, there might be a small charge for the temporary use of an Amazon EBS snapshot\. For more information about Amazon S3 pricing, see [Amazon Simple Storage Service Pricing](https://aws.amazon.com/s3/pricing/)\.

**Topics**
+ [Prerequisites](#vmexport-prerequisites)
+ [Considerations for Instance Export](#vmexport-limits)
+ [Start an Instance Export Task](#export-instance)
+ [Monitor an Instance Export Task](#vmexport-monitor)
+ [Cancel an Instance Export Task](#vmexport-cancel)

## Prerequisites<a name="vmexport-prerequisites"></a>

To export a VM from Amazon EC2, first meet the following prerequisites\.
+ Install the AWS CLI\. For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.
+ Create an Amazon S3 bucket for storing the exported instances or choose an existing bucket\. The bucket must be in the Region where you want export your VMs\. For more information, see the [Amazon Simple Storage Service Console User Guide](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/)\.
+ Attach an access control list \(ACL\) to your S3 bucket containing the following grants\. For more information, see [Managing Access with ACLs](https://docs.aws.amazon.com/AmazonS3/latest/dev/S3_ACLs_UsingACLs.html) in the *Amazon Simple Storage Service Developer Guide*\.
  + 

    For `Grantee`, provide the appropriate Region\-specific canonical account ID:  
**Africa \(Cape Town\)**  
`3f7744aeebaf91dd60ab135eb1cf908700c8d2bc9133e61261e6c582be6e33ee`  
**Asia Pacific \(Hong Kong\)**  
`97ee7ab57cc9b5034f31e107741a968e595c0d7a19ec23330eae8d045a46edfb`  
**Europe \(Milan\)**  
`04636d9a349e458b0c1cbf1421858b9788b4ec28b066148d4907bb15c52b5b9c`  
**Middle East \(Bahrain\)**  
`aa763f2cf70006650562c62a09433f04353db3cba6ba6aeb3550fdc8065d3d9f`  
**China \(Beijing\)**  
`834bafd86b15b6ca71074df0fd1f93d234b9d5e848a2cb31f880c149003ce36f`  
**AWS GovCloud \(US\)**  
`af913ca13efe7a94b88392711f6cfc8aa07c9d1454d4f190a624b126733a5602`  
**All other Regions**  
`c4d8eabf8db69dbe46bfe0e517100c554f01200b104d59cd408e777ba442a322`
  + `READ_ACP` permission
  + `WRITE` permission

## Considerations for Instance Export<a name="vmexport-limits"></a>

Exporting instances and volumes is subject to the following limitations:
+ You must export your instances and volumes to one of the following image formats that your virtualization environment supports:
  + Open Virtual Appliance \(OVA\), which is compatible with VMware vSphere versions 4, 5, and 6\.
  + Virtual Hard Disk \(VHD\), which is compatible with Citrix Xen and Microsoft Hyper\-V virtualization products\.
  + Stream\-optimized ESX Virtual Machine Disk \(VMDK\), which is compatible with VMware ESX and VMware vSphere versions 4, 5, and 6\.
+ You can't export an instance if it contains third\-party software provided by AWS\. For example, VM Export cannot export Windows or SQL Server instances, or any instance created from an image in the AWS Marketplace\.
+ You can't export an instance with encrypted EBS snapshots in the block device mapping\.
+ You can't export an instance with instance store volumes in the block device mapping\.
+ You can only export EBS volumes that are specified in the block device mapping, not EBS volumes attached after instance launch\.
+ You can't export an instance launched from an imported image if you deleted the AMI or the EBS snapshot for the AMI\. To work around the issue, create an AMI from the instance and export the AMI\.
+ You can't export an instance that has more than one virtual disk\.
+ You can't export an instance that has more than one network interface\.
+ You can't export an instance from Amazon EC2 if you've shared it from another AWS account\.
+ By default, you can't have more than 5 conversion tasks per Region in progress at the same time\. This limit is adjustable up to 20\.
+ VMs with volumes larger than 1 TiB are not supported\.
+ You can export a volume to either an unencrypted S3 bucket or to a bucket encrypted using SSE\-S3\. You cannot export to an S3 bucket encrypted using SSE\-KMS\.

## Start an Instance Export Task<a name="export-instance"></a>

To export your instance, use the [create\-instance\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) command\. The exported file is written to the specified S3 bucket in the following S3 key: *prefix*export\-i\-*xxxxxxxxxxxxxxxxx*\.*format* \(for example, my\-export\-bucket/vms/export\-i\-1234567890abcdef0\.ova\)\.

```
aws ec2 create-instance-export-task --instance-id instance-id --target-environment vmware --export-to-s3-task file://C:\file.json
```

The file `file.json` is a JSON document that contains the required information\.

```
{
    "ContainerFormat": "ova",
    "DiskImageFormat": "VMDK",
    "S3Bucket": "my-export-bucket",
    "S3Prefix": "vms/"
}
```

## Monitor an Instance Export Task<a name="vmexport-monitor"></a>

To monitor the export of your instance, use the following [describe\-export\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-export-tasks.html) command:

```
aws ec2 describe-export-tasks --export-task-ids export-i-1234567890abcdef0
```

## Cancel an Instance Export Task<a name="vmexport-cancel"></a>

If you need to, you can use the following [cancel\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-export-task.html) command to cancel the export of an instance that is in progress\.

```
aws ec2 cancel-export-task --export-task-id export-i-1234567890abcdef0
```

This command removes all artifacts of the export, including any partially created Amazon S3 objects\. If the export task is complete or is in the process of transferring the final disk image, the command fails and returns an error\.