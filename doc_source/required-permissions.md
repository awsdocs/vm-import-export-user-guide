# Required permissions for VM Import/Export<a name="required-permissions"></a>

VM Import/Export requires permissions for both AWS Identity and Access Management users and a service role to perform certain operations on your behalf\.

**Contents**
+ [Required permissions for IAM users](#iam-permissions-image)
+ [Required service role](#vmimport-role)

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

1. For additional security controls, context keys such as `aws:SourceAccount` and `aws:SourceArn` can be added to the trust policy for this newly created role\. VM Import/Export will publish the `SourceAccount` and `SourceArn` keys as specified in the example below to assume this role:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "vmie.amazonaws.com"
               },
               "Action": "sts:AssumeRole",
               "Condition": {
                   "StringEquals": {
                       "sts:Externalid": "vmimport",
                       "aws:SourceAccount": "111122223333"
                   },
                   "ArnLike": {
                       "aws:SourceArn": "arn:aws:vmie:*:111122223333:*"
                   }
               }
           }
       ]
   }
   ```