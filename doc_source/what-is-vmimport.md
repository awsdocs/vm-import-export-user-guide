# What Is VM Import/Export?<a name="what-is-vmimport"></a>

VM Import/Export enables you to import virtual machine \(VM\) images from your existing virtualization environment to Amazon EC2, and then export them back\. This enables you to migrate applications and workloads to Amazon EC2, copy your VM image catalog to Amazon EC2, or create a repository of VM images for backup and disaster recovery\.

For more information, see [VM Import/Export](https://aws.amazon.com/ec2/vm-import/)\.

**Note**  
For most VM import needs, we recommend that you use the AWS Server Migration Service\. AWS SMS automates the import process \(reducing the workload of migrating large VM infrastructures\), adds support for incremental updates of changing VMs, and converts your imported VMs into ready\-to\-use Amazon machine images \(AMIs\)\. To get started with AWS SMS, see [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)\.

## Features of VM Import/Export<a name="vmimport-features"></a>

VM Import provides the following features:
+ The ability to import a VM from your virtualization environment to Amazon EC2 as an Amazon Machine Image \(AMI\)\. You can launch EC2 instances from your AMI any time\.
+ The ability to import a VM from your virtualization environment to Amazon EC2 as an EC2 instance\. The instance is initially in a `stopped` state\. You can create an AMI from the instance\.
+ The ability to export a VM that was previously imported from your virtualization environment\.
+ The ability to import disks as Amazon EBS snapshots\.

## How to Get Started with VM Import/Export<a name="vmimport-where-do-i-go"></a>

First, you must decide whether you will import your VMs as AMIs or instances\. To get started, read about how image import and instance import work\. You can also read through the prerequisites and limitations of each method\. For more information, see:
+ [How VM Import/Export Works](how-vm-import-export-works.md)
+ [Importing a VM as an Image Using VM Import/Export](vmimport-image-import.md)
+ [Importing a VM as an Instance Using VM Import/Export](vmimport-instance-import.md)

## Accessing VM Import/Export<a name="vmimport-interface"></a>

You can access VM Import/Export using the following interfaces:

**AWS Command Line Interface \(CLI\)**  
Provides commands for a broad set of AWS products, and is supported on Windows, Mac, and Linux\. To get started, see [AWS Command Line Interface User Guide](http://docs.aws.amazon.com/cli/latest/userguide/)\. For more information about the commands for Amazon EC2, see [ec2](http://docs.aws.amazon.com/cli/latest/reference/ec2/index.html) in the *AWS CLI Command Reference*\.

**AWS Tools for Windows PowerShell**  
Provides commands for a broad set of AWS products for those who script in the PowerShell environment\. To get started, see the [AWS Tools for Windows PowerShell User Guide](http://docs.aws.amazon.com/powershell/latest/userguide/)\. For more information about the cmdlets for Amazon EC2, see the [AWS Tools for PowerShell Cmdlet Reference](http://docs.aws.amazon.com/powershell/latest/reference/Index.html)\.

**Amazon EC2 API**  
Amazon EC2 provides a Query API\. These requests are HTTP or HTTPS requests that use the HTTP verbs GET or POST and a Query parameter named `Action`\. For more information about the API actions for Amazon EC2, see [Actions](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-apis.html) in the *Amazon EC2 API Reference*\.

**AWS SDKs and Tools**  
If you prefer to build applications using language\-specific APIs instead of submitting a request over HTTP or HTTPS, AWS provides libraries, sample code, tutorials, and other resources for software developers\. These libraries provide basic functions that automate tasks such as cryptographically signing your requests, retrying requests, and handling error responses, making it is easier for you to get started\. For more information, see [AWS SDKs and Tools](http://aws.amazon.com/tools/)\.

## Pricing<a name="vmimport-pricing"></a>

With Amazon Web Services, you pay only for what you use\. There is no additional fee to use VM Import/Export\. You pay the standard fees for the S3 buckets and EBS volumes used during the import and export processes, and for the EC2 instances that you run\.

## Related Services<a name="vmimport-related-services"></a>

VM Import/Export works with the following services:
+ To plan your application migration, you can use the Application Discovery Service\. This service identifies assets, maps the dependencies between assets, and provides an inventory of assets that you can query\. For more information, see the [Application Discovery Service User Guide](http://docs.aws.amazon.com/application-discovery/latest/userguide/)\.
+ If you're using VMware vSphere, you can use the AWS Connector for vCenter to export a VM from VMware and import it into Amazon EC2\. For more information, see [Migrating Your Virtual Machine to Amazon EC2 Using AWS Connector for vCenter](http://docs.aws.amazon.com/amp/latest/userguide/migrate-vms.html) in the *AWS Management Portal for vCenter User Guide*\.
+ If you use Microsoft Systems Center, you can use AWS Systems Manager for Microsoft SCVMM to import Windows VMs from SCVMM to Amazon EC2\. For more information, see [Importing Your Virtual Machine Using AWS Systems Manager for Microsoft SCVMM](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/scvmm-import-vm.html) in the *Amazon EC2 User Guide for Windows Instances*\.