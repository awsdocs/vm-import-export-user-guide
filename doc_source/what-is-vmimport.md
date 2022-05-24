# What is VM Import/Export?<a name="what-is-vmimport"></a>

VM Import/Export enables you to import virtual machine \(VM\) images from your existing virtualization environment to Amazon EC2, and then export them back\. This enables you to migrate applications and workloads to Amazon EC2, copy your VM image catalog to Amazon EC2, or create a repository of VM images for backup and disaster recovery\.

For more information, see [VM Import/Export](https://aws.amazon.com/ec2/vm-import/)\.

## Features of VM Import/Export<a name="vmimport-features"></a>

VM Import provides the following features:
+ The ability to import a VM from your virtualization environment to Amazon EC2 as an Amazon Machine Image \(AMI\)\. You can launch EC2 instances from your AMI any time\.
+ The ability to import a VM from your virtualization environment to Amazon EC2 as an EC2 instance\. The instance is initially in a `stopped` state\. You can create an AMI from the instance\.
+ The ability to export a VM that was previously imported from your virtualization environment\.
+ The ability to import disks as Amazon EBS snapshots\.
+ VM import supports ENA drivers for Linux\. ENA support will be enabled only if the original VM has ENA and/or NVMe drivers installed\. We recommend installing the latest drivers\.

## How to get started with VM Import/Export<a name="vmimport-where-do-i-go"></a>

First, you must decide whether you will import your VMs as AMIs or instances\. To get started, read about how image import and instance import work\. You can also read through the prerequisites and limitations of each method\. For more information, see:
+ [How VM Import/Export works](how-vm-import-export-works.md)
+ [Importing a VM as an image using VM Import/Export](vmimport-image-import.md)
+ [Importing a disk as a snapshot using VM Import/Export](vmimport-import-snapshot.md)

## Accessing VM Import/Export<a name="vmimport-interface"></a>

You can access VM Import/Export using the following interfaces:

**AWS Command Line Interface \(CLI\)**  
Provides commands for a broad set of AWS products, and is supported on Windows, Mac, and Linux\. To get started, see [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\. For more information about the commands for Amazon EC2, see [ec2](https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html) in the *AWS CLI Command Reference*\.

**AWS Tools for Windows PowerShell**  
Provides commands for a broad set of AWS products for those who script in the PowerShell environment\. To get started, see the [AWS Tools for Windows PowerShell User Guide](https://docs.aws.amazon.com/powershell/latest/userguide/)\. For more information about the cmdlets for Amazon EC2, see the [AWS Tools for PowerShell Cmdlet Reference](https://docs.aws.amazon.com/powershell/latest/reference/Index.html)\.

**Amazon EC2 API**  
Amazon EC2 provides a Query API\. These requests are HTTP or HTTPS requests that use the HTTP verbs GET or POST and a Query parameter named `Action`\. For more information about the API actions for Amazon EC2, see [Actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-apis.html) in the *Amazon EC2 API Reference*\.

**AWS SDKs and Tools**  
If you prefer to build applications using language\-specific APIs instead of submitting a request over HTTP or HTTPS, AWS provides libraries, sample code, tutorials, and other resources for software developers\. These libraries provide basic functions that automate tasks such as cryptographically signing your requests, retrying requests, and handling error responses, making it is easier for you to get started\. For more information, see [AWS SDKs and Tools](http://aws.amazon.com/tools/)\.  
In [ supported AWS Regions](https://docs.aws.amazon.com/cloudshell/latest/userguide/supported-aws-regions.html), you can also use [AWS CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html) for a browser\-based, pre\-authenticated shell that launches directly from the AWS Management Console\.

## Pricing<a name="vmimport-pricing"></a>

With Amazon Web Services, you pay only for what you use\. There is no additional fee to use VM Import/Export\. You pay the standard fees for the S3 buckets and EBS volumes used during the import and export processes, and for the EC2 instances that you run\.

## Related services<a name="vmimport-related-services"></a>

Consider the following services as you plan your migration to AWS:
+ You can use the Application Discovery Service to gather information about your data center, such as server utilization data and dependency mappings, so that you can view information about your workloads\. For more information, see the [Application Discovery Service User Guide](https://docs.aws.amazon.com/application-discovery/latest/userguide/)\.
+ If you use VMware vSphere, Microsoft Hyper\-V, or Microsoft Azure, you can use AWS Application Migration Service to automate the migration of your virtual machines to AWS\. For more information, see the [Application Migration Service User Guide](https://docs.aws.amazon.com/mgn/latest/ug/what-is-application-migration-service.html)\.
+ If you use Microsoft Systems Center, you can use AWS Systems Manager for Microsoft SCVMM to import Windows VMs from SCVMM to Amazon EC2\. For more information, see [Import your virtual machine using AWS Systems Manager for Microsoft SCVMM](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/scvmm-import-vm.html) in the *Amazon EC2 User Guide for Windows Instances*\.