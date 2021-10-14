# Exporting a VM directly from an Amazon Machine Image \(AMI\)<a name="vmexport_image"></a>

Exporting a VM file based on an Amazon Machine Image \(AMI\) is useful when you want to deploy a new, standardized instance in your on\-site virtualization environment\. You can export most AMIs to Citrix Xen, Microsoft Hyper\-V, or VMware vSphere\.

When you export an image, you are charged the standard Amazon S3 rates for the bucket where the exported VM is stored\. In addition, there might be a small charge for the temporary use of an Amazon EBS snapshot\. For more information about Amazon S3 pricing, see [Amazon Simple Storage Service Pricing](https://aws.amazon.com/s3/pricing/)\.

**Topics**
+ [Prerequisites](#prerequisites-image-export)
+ [Considerations for image export](#limits-image-export)
+ [Start an export image task](#start-image-export)
+ [Monitor an export image task](#monitor-image-export)
+ [Cancel an export image task](#cancel-image-export)

## Prerequisites<a name="prerequisites-image-export"></a>

To export a VM from Amazon EC2, first meet the following prerequisites\.
+ Install the AWS CLI\. For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.
+ Create an Amazon S3 bucket for storing the exported images or choose an existing bucket\. The bucket must be in the Region where you want to export your VMs\. For more information about S3 buckets, see the [Amazon Simple Storage Service Console User Guide](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/)\.
+ Create an IAM role named `vmimport`\. For more information, see [Required service role](vmie_prereqs.md#vmimport-role)\.

## Considerations for image export<a name="limits-image-export"></a>

Exporting images and volumes is subject to the following limitations:
+ You must export to one of the following image formats that your virtualization environment supports:
  + Virtual Hard Disk \(VHD\), which is compatible with Citrix Xen and Microsoft Hyper\-V virtualization products\.
  + Stream\-optimized ESX Virtual Machine Disk \(VMDK\), which is compatible with VMware ESX and VMware vSphere versions 4, 5, and 6\.
  + Raw format\.

  To convert exported VMDK files to OVF, use the [VMware OVF Tool](https://www.vmware.com/support/developer/ovf/)\.
+ You can't export an image if it contains third\-party software provided by AWS\. For example, VM Export cannot export Windows or SQL Server images, or any image created from an image in the AWS Marketplace\.
+ You can't export an image with encrypted EBS snapshots in the block device mapping\.
+ You can only export EBS data volumes that are specified in the block device mapping, not EBS volumes attached after instance launch\.
+ You can't export an image from Amazon EC2 if you've shared it from another AWS account\.
+ You can't have multiple export image tasks in progress for the same AMI at the same time\.
+ By default, you can't have more than 5 conversion tasks per Region in progress at the same time\. This limit is adjustable up to 20\.
+ VMs with volumes larger than 1 TiB are not supported\.
+ You can export a volume to either an unencrypted Amazon S3 bucket or to a bucket encrypted using SSE\-S3 encryption\. You cannot export to an S3 bucket encrypted using SSE\-KMS encryption\.

## Start an export image task<a name="start-image-export"></a>

To export your image, use the [export\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/export-image.html) command\. The exported file is written to the specified S3 bucket using the following S3 key: *prefix*export\-ami\-*id*\.*format* \(for example, my\-export\-bucket/exports/export\-ami\-1234567890abcdef0\.ova\)\.

```
aws ec2 export-image --image-id ami-id --disk-image-format VMDK --s3-export-location S3Bucket=my-export-bucket,S3Prefix=exports/
```

## Monitor an export image task<a name="monitor-image-export"></a>

To monitor the export of your image, use the following [describe\-export\-image\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-export-image-tasks.html) command:

```
aws ec2 describe-export-image-tasks --export-image-task-ids export-ami-1234567890abcdef0
```

The following is an example response\. The status shown is `active`, which means that the export task is in progress\. The image is ready to use when the status is `completed`\.

```
{
  "ExportImageTasks": [
      {
          "ExportImageTaskId": "export-ami-1234567890abcdef0"
          "Progress": "21",
          "S3ExportLocation": {
              "S3Bucket": "my-export-bucket",
              "S3Prefix": "exports/"
          },
          "Status": "active",
          "StatusMessage": "updating"
      }
  ]
}
```

## Cancel an export image task<a name="cancel-image-export"></a>

If you need to, you can use the following [cancel\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-export-task.html) command to cancel the export of an image that is in progress\.

```
aws ec2 cancel-export-task --export-task-id export-ami-1234567890abcdef0
```

If the export task is complete or is in the process of transferring the final disk image, the command fails and returns an error\.