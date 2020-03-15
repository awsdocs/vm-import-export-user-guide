# Importing a Disk as a Snapshot Using VM Import/Export<a name="vmimport-import-snapshot"></a>

VM Import/Export enables you to import your disks as Amazon EBS snapshots\. After the snapshot is created, you can create an EBS volume from the snapshot, and then attach the volume to an EC2 instance\.

An imported snapshot has an arbitrary volume ID that should not be used for any purpose\.

## Prerequisites<a name="import-snapshot-prerequisites"></a>
+ The following disk formats are supported: Virtual Hard Disk \(VHD/VHDX\), ESX Virtual Machine Disk \(VMDK\), and raw\.
+ You must first upload your disks to Amazon S3\.
+ If you have not already installed the AWS CLI on the computer you'll use to run the import commands, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

## Start an Import Snapshot Task<a name="start-import-task"></a>

Use the following [import\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-snapshot.html) command to import a disk\. You can specify the URL of the S3 bucket, or provide the S3 bucket name and key\.

```
aws ec2 import-snapshot --description "My server VM" --disk-container "file://C:\import\containers.json"
```

The file `containers.json` is a JSON document that contains the required information\.

```
{
    "Description": "My server VMDK",
    "Format": "VMDK",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "vms/my-server-vm.vmdk"
    }
}
```

The following is an example response:

```
{
    "Description": "My server VM",
    "ImportTaskId": "import-snap-1234567890abcdef0",
    "SnapshotTaskDetail": {
        "Description": "My server VMDK",
        "DiskImageSize": "0.0",
        "Format": "VMDK",
        "Progress": "3",
        "Status": "active",
        "StatusMessage": "pending",
        "UserBucket": {
            "S3Bucket": "my-import-bucket",
            "S3Key": "vms/my-server-vm.vmdk"
        }
    }
}
```

## Monitor an Import Snapshot Task<a name="check-status-import-task"></a>

Use the [describe\-import\-snapshot\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-snapshot-tasks.html) command to check the status of an import snapshot task\.

```
aws ec2 describe-import-snapshot-tasks --import-task-ids import-snap-1234567890abcdef0
```

The following is an example response\. The status shown is `active`, which means that the import is in progress\. The snapshot is ready to use when the status is `completed`\.

```
{
    "ImportSnapshotTasks": [
        {
            "Description": "My server VM",
            "ImportTaskId": "import-snap-1234567890abcdef0",
            "SnapshotTaskDetail": {
                "Description": "My server VMDK",
                "DiskImageSize": "3.115815424E9",
                "Format": "VMDK",
                "Progress": "22",
                "Status": "active",
                "StatusMessage": "downloading/converting",
                "UserBucket": {
                    "S3Bucket": "my-import-bucket",
                    "S3Key": "vms/my-server-vm.vmdk"
                },
            }
        }
    ]
}
```

## Cancel an Import Snapshot Task<a name="cancel-import-task"></a>

If you need to, you can cancel an import task that is in progress using the [cancel\-import\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-import-task.html) command\.

```
aws ec2 cancel-import-task --import-task-id import-snap-1234567890abcdef0
```

## Next Steps<a name="import-snapshot-next-steps"></a>

You can create one or more EBS volumes from an EBS snapshot\. You can attach each EBS volume to a single EC2 instance\.

The following procedure shows how to create a volume and attach it to an instance using the AWS CLI\. Alternatively, you could use the AWS Management Console\.

**To create a volume and attach it to an EC2 instance**

1. Use the [describe\-import\-snapshot\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-import-snapshot-tasks.html) command to determine the ID of the snapshot that was created by the import task\.

1. Use the following [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command to create a volume from the snapshot\. You must select the Availability Zone of the instance to which you'll attach the volume\.

   ```
   aws ec2 create-volume --availability-zone us-east-1a --snapshot-id snap-1234567890abcdef0
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

1. Use the following [attach\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-volume.html) command to attach the EBS volume that you created in the previous step to one of your existing instances\.

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

1. Mount the attached volume\. For more information, see the documentation for the operating system for your instance\.