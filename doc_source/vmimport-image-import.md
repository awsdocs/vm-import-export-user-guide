# Importing a VM as an Image Using VM Import/Export<a name="vmimport-image-import"></a>

You can use VM Import/Export to import virtual machine \(VM\) images from your virtualization environment to Amazon EC2 as Amazon Machine Images \(AMI\), which you can use to launch instances\. Subsequently, you can export the VM images from an instance back to your virtualization environment\. This enables you to leverage your investments in the VMs that you have built to meet your IT security, configuration management, and compliance requirements by bringing them into Amazon EC2\.

**Note**  
For most VM import needs, we recommend that you use the AWS Server Migration Service\. AWS SMS automates the import process \(reducing the workload of migrating large VM infrastructures\), adds support for incremental updates of changing VMs, and converts your imported VMs into ready\-to\-use Amazon machine images \(AMIs\)\. To get started with AWS SMS, see [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)\.

**Topics**
+ [Export Your VM from its Virtualization Environment](#export-vm-image)
+ [Import Your VM as an Image](#import-vm-image)

## Export Your VM from its Virtualization Environment<a name="export-vm-image"></a>

After you have prepared your VM for export, you can export it from your virtualization environment\. When importing a VM as an image, you can import disks in the following formats: Open Virtualization Archive \(OVA\), Virtual Machine Disk \(VMDK\), Virtual Hard Disk \(VHD/VHDX\), and raw\. With some virtualization environments, you would export to Open Virtualization Format \(OVF\), which typically includes one or more VMDK, VHD, or VHDX files\.

For more information, see the documentation for your virtualization environment\. For example:
+ **VMware** — [Export an OVF Template](http://pubs.vmware.com/vsphere-4-esx-vcenter/topic/com.vmware.vsphere.vmadmin.doc_41/vc_client_help/importing_and_exporting_virtual_appliances/t_export_a_virtual_machine.html) on the VMware website
+ **Citrix** — [Export VMs as OVF/OVA](http://docs.citrix.com/en-us/xencenter/6-2/xs-xc-vms-exportimport/xs-xc-vms-export-ovf.html) on the Citrix website
+ **Microsoft Hyper\-V** — [Overview of exporting and importing a virtual machine](https://technet.microsoft.com/en-us/library/hh831535.aspx) on the Microsoft website

## Import Your VM as an Image<a name="import-vm-image"></a>

After exporting your VM from your virtualization environment, you can import it to Amazon EC2\. The import process is the same regardless of the origin of the VM\.

**Topics**
+ [Prerequisites](#import-image-prereqs)
+ [Upload the Image to Amazon S3](#upload-image)
+ [Import the VM](#import-vm)
+ [Check the Status of the Import Task](#check-import-task-status)
+ [\(Optional\) Cancel an Import Task](#cancel-upload)
+ [Next Steps](#next-steps)

### Prerequisites<a name="import-image-prereqs"></a>

You must provide an Amazon S3 bucket and an IAM role named `vmimport`\.

#### Amazon S3 Bucket<a name="vmimport-s3-bucket"></a>

VM Import requires an Amazon S3 bucket to store your disk images, in the region where you want to import your VMs\. You can create a bucket as follows, or use an existing bucket if you prefer\.

**\(Optional\) To create an S3 bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create Bucket**\.

1. In the **Create a Bucket** dialog box, do the following:

   1. For **Bucket Name**, type a name for your bucket\. This name must be unique across all existing bucket names in Amazon S3\. In some regions, there might be additional restrictions on bucket names\. For more information, see [Bucket Restrictions and Limitations](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

   1. For **Region**, select the region that you want for your AMI\.

   1. Choose **Create**\.

#### VM Import Service Role<a name="vmimport-service-role"></a>

VM Import requires a role to perform certain operations in your account, such as downloading disk images from an Amazon S3 bucket\. You must create a role named `vmimport` with a trust relationship policy document that allows VM Import to assume the role, and you must attach an IAM policy to the role\.

**To create the service role**

1. Create a file named `trust-policy.json` with the following policy:

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

   You can save the file anywhere on your computer\. Take note of the location of the file, because you'll specify the file in the next step\.

1. Use the [create\-role](http://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html) command to create a role named `vmimport` and give VM Import/Export access to it\. Ensure that you specify the full path to the location of the `trust-policy.json` file, and that you prefix `file://` to it:

   ```
   aws iam create-role --role-name vmimport --assume-role-policy-document file://trust-policy.json
   ```

**Note**  
If you encounter an error stating that "This policy contains invalid Json," double\-check that the path to the JSON file is provided correctly\.

1. Create a file named `role-policy.json` with the following policy, where *disk\-image\-file\-bucket* is the bucket where the disk images are stored:

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Action":[
               "s3:GetBucketLocation",
               "s3:ListBucket" 
            ],
            "Resource":[
               "arn:aws:s3:::disk-image-file-bucket",
            ]
         },
         {  "Effect":"Allow",
            "Action":[
               "s3:GetObject"
            ],
            "Resource":[
                "arn:aws:s3:::disk-image-file-bucket/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "ec2:ModifySnapshotAttribute",
               "ec2:CopySnapshot",
               "ec2:ImportImage",
               "ec2:RegisterImage",
               "ec2:Describe*"
            ],
            "Resource":"*"
         }
      ]
   }
   ```

1. Use the following [put\-role\-policy](http://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command to attach the policy to the role created above\. Ensure that you specify the full path to the location of the `role-policy.json` file\.

   ```
   aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json
   ```

For more information about IAM roles, see [IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/roles-toplevel.html) in the *IAM User Guide*\.

### Upload the Image to Amazon S3<a name="upload-image"></a>

Upload your VM image file to your Amazon S3 bucket using the upload tool of your choice\. For information about uploading files through the S3 console, see [Uploading Objects into Amazon S3](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/UploadingObjectsintoAmazonS3.html)\. For information about the Enhanced Uploader Java applet, see [Using the Enhanced Uploader](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/enhanced-uploader.html)\.

### Import the VM<a name="import-vm"></a>

After you upload your VM image file to Amazon S3, you can use the AWS CLI to import the image\. These tools accept either the Amazon S3 bucket and path to the file or a URL for a public Amazon S3 file\. Private Amazon S3 files require a [signed GET URL]( http://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html)\.

The following examples use the AWS CLI command [import\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) to create import tasks\.

**Example 1: Import an OVA**

```
aws ec2 import-image --description "Windows 2008 OVA" --license-type <value> --disk-containers file://containers.json
```

The following is an example `containers.json` file\.

```
[
  {
    "Description": "Windows 2008 OVA",
    "Format": "ova",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "vms/my-windows-2008-vm.ova"
    }
}]
```

**Example 2: Import Multiple Disks**

```
$ C:\> aws ec2 import-image --description "Windows 2008 VMDKs" --license-type <value> --disk-containers file://containers.json
```

The following is an example `containers.json` file\.

```
[
  {
    "Description": "First disk",
    "Format": "vmdk",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "disks/my-windows-2008-vm-disk1.vmdk"
    }
  },          
  {
    "Description": "Second disk",
    "Format": "vmdk",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "disks/my-windows-2008-vm-disk2.vmdk"
    }
  }
]
```

### Check the Status of the Import Task<a name="check-import-task-status"></a>

Use the [describe\-import\-image\-tasks](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-image-tasks.html) command to return the status of an import task\.

Status values include the following:
+ `active` — The import task is in progress\.
+ `deleting` — The import task is being canceled\.
+ `deleted` — The import task is canceled\.
+ `updating` — Import status is updating\.
+ `validating` — The imported image is being validated\.
+ `validated` — The imported image was validated\.
+ `converting` — The imported image is being converted into an AMI\.
+ `completed` — The import task is completed and the AMI is ready to use\.

```
aws ec2 describe-import-image-tasks --import-task-ids import-ami-abcd1234
```

You can also use the older EC2 CLI command ec2 describe\-conversion\-tasks to achieve the same purpose:

```
aws ec2 describe-conversion-tasks --region <region>
```

### \(Optional\) Cancel an Import Task<a name="cancel-upload"></a>

Use the [cancel\-import\-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-import-task.html) command to cancel an active import task\.

```
aws ec2 cancel-import-task --import-task-id import-ami-abcd1234
```

### Next Steps<a name="next-steps"></a>

Now that you have an AMI, you can launch it as an instance or copy it to another region\. For more information, see the following topics in the Amazon EC2 documentation\.

**Windows**
+ [Launching an Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/launching-instance.html)
+ [Installing the Latest Version of EC2Config](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html) \(recommended\)
+ [Copying an AMI](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/CopyingAMIs.html)

**Linux**
+ [Launching an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html)

+ [Copying an AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html)

