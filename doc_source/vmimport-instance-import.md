# Importing a VM as an Instance Using VM Import/Export<a name="vmimport-instance-import"></a>

You can use VM Import/Export to import virtual machine \(VM\) images from your virtualization environment to Amazon EC2 as instances\. Subsequently, you can export the VM images from the instance back to your virtualization environment\. This enables you to leverage your investments in the VMs that you have built to meet your IT security, configuration management, and compliance requirements by bringing them into Amazon EC2\. 

**Note**  
For most VM import needs, we recommend that you use the AWS Server Migration Service\. AWS SMS automates the import process \(reducing the workload of migrating large VM infrastructures\), adds support for incremental updates of changing VMs, and converts your imported VMs into ready\-to\-use Amazon machine images \(AMIs\)\. To get started with AWS SMS, see [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)\.

To import your VM to Amazon EC2 as an instance, you must first export it from your virtualization environment, and then import it to Amazon EC2 using the Amazon EC2 CLI\. For more information, see [Importing a Virtual Machine Using the Amazon EC2 CLI](http://docs.aws.amazon.com/AWSEC2/latest/CommandLineReference/ec2-cli-vmimport-export.html) in the *Amazon EC2 Command Line Reference*\.

Note that the AWS CLI does not support importing a VM as an instance\. If you prefer to use the AWS CLI, you must import the VM as an Amazon Machine Image \(AMI\) instead\. For more information, see [Importing a VM as an Image Using VM Import/Export](vmimport-image-import.md)\.

If you're importing a Windows instance that uses the bring your own license \(BYOL\) model, you must import the VM as an AMI instead\. For more information, see [Importing a VM as an Image Using VM Import/Export](vmimport-image-import.md)\.

If you're importing a VM from VMware vCenter, you can alternatively use the AWS Connector for vCenter to export a VM from VMware and import it to Amazon EC2\. For more information, see [Migrating Your Virtual Machine to Amazon EC2 Using AWS Connector for vCenter](http://docs.aws.amazon.com/amp/latest/userguide/migrate-vms.html) in the *AWS Management Portal for vCenter User Guide*\.