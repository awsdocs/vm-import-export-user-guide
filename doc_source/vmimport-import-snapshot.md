# Importing a Disk as a Snapshot Using VM Import/Export<a name="vmimport-import-snapshot"></a>

VM Import/Export enables you to import your disks as Amazon EBS snapshots\. You can create an EBS volume from an EBS snapshot, and then attach the volume to an instance\.

**Prerequisites**

+ The following disk formats are supported: Virtual Hard Disk \(VHD/VHDX\), ESX Virtual Machine Disk \(VMDK\), and raw\.

+ You must first upload your disks to Amazon S3\.

**To import a disk image**

1. Use the following [import\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/ec2/import-snapshot.html) command to import a disk\. You can specify the URL of the S3 bucket, or provide the S3 bucket name and key\.

   ```
   aws ec2 import-snapshot --description "Windows 2008 VMDK" --disk-container file://containers.json
   ```

   The file `containers.json` is a JSON document that contains required information\.

   ```
   {
       "Description": "Windows 2008 VMDK",
       "Format": "vmdk",
       "UserBucket": {
           "S3Bucket": "mys3bucket",
           "S3Key": "vms/Win_2008_Server_Enterprise_R2_64-bit.vmdk"
       }
   }
   ```

   The following is an example response:

   ```
   {
       "ImportTaskId": "import-snap-abcd1234",
       "SnapshotTaskDetail":[
           {
               "DiskImageSize": "0.0",
               "Progress": "3",
               "Status": "active",
               "Description": "Windows 2008 VMDK",
               "UserBucket": {
                   "S3Bucket": "mys3bucket",
                   "S3Key": "vms/Win_2008_Server_Enterprise_R2_64-bit.vmdk"
               },
               "StatusMessage": "pending"
           }
       ],
       "Description": "Windows 2008 VMDK"
   }
   ```

1. Use the [describe\-import\-snapshot\-tasks](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-snapshot-tasks.html) command to confirm that your snapshot imported successfully\.

   ```
   aws ec2 describe-import-snapshot-tasks --import-task-ids import-snap-fgr1mmg7
   ```

   The following is an example response\. The status shown is `active`, which means the import is in progress\.

   ```
   {
       "ImportSnapshotTasks": [
           {
               "SnapshotTaskDetail": [
                   "DiskImageSize": "3.115815424E9",
                   "Progress": "22",
                   "Status": "active",
                   "Description": "Windows 2008 VMDK",
                   "Format": "VMDK",
                   "UserBucket": {
                       "S3Bucket": "mys3bucket",
                       "S3Key: "vms/Win_2008_Server_Enterprise_R2_64-bit.vmdk"
                   }
                   "StatusMessage": "Validated"
               ],
               "ImportTaskId": "import-snap-fgr1mmg7",
               "Description": "Windows 2008 VMDK"
           }
       ]
   }
   ```

1. Use the following [create\-volume](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command to create a volume from the snapshot\. You must select the Availability Zone of the instance to which you'll attach the volume\.

   ```
   aws ec2 create-volume --availability-zone us-east-1a -snapshot-id snap-1234567890abcdef0
   ```

   The following is example output:

   ```
   {
       "AvailabilityZone": "us-east-1a",
       "VolumeId": "vol-1234567890abcdef0",
       "State": "creating",
       "SnapshotId": "snap-1234567890abcdef0"
   }
   ```

1. Use the following [attach\-volume](http://docs.aws.amazon.com/cli/latest/reference/ec2/attach-volume.html) command to attach the EBS volume that you created in the previous step to one of your existing instances\.

   ```
   aws ec2 attach-volume --volume-id vol-1234567890abcdef0 --instance-id i-1234567890abcdef0 --device /dev/sdf
   ```

   The following is example output:

   ```
   {
       "AttachTime": "YYYY-MM-DDTHH:MM:SS.000Z",
       "InstanceId": "i-1234567890abcdef0",
       "VolumeId": "vol-1234567890abcdef0",
       "State": "attaching",
       "Device": "/dev/sdf"
   }
   ```

## Canceling an Import Task<a name="cancel-import-task"></a>

You can cancel an import task that is in progress\. 

**To cancel a snapshot import task**

1. Use the [describe\-import\-snapshot\-tasks](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-snapshot-tasks.html) command to determine the ImportTaskId of the import task you want to cancel\. 

   ```
   aws ec2 describe-import-snapshot-tasks
   ```

1. Cancel the task with [cancel\-import\-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-import-task.html)\. command\.

   ```
   aws ec2 cancel-import-task --import-task-id "import-snap-abcd1234"
   ```