# Importing a VM as an Instance Using VM Import/Export<a name="vmimport-instance-import"></a>

You can use VM Import/Export to import virtual machine \(VM\) images from your virtualization environment to Amazon EC2 as instances\. Subsequently, you can export the VM images from the instance back to your virtualization environment\. This enables you to leverage your investments in the VMs that you have built to meet your IT security, configuration management, and compliance requirements by bringing them into Amazon EC2\.

The AWS CLI does not support importing a VM as an instance\. If you prefer to use the AWS CLI, you must import the VM as an Amazon Machine Image \(AMI\) instead\. If you're importing a Windows instance that uses the bring your own license \(BYOL\) model, you must import the VM as an AMI\. For more information, see [Importing a VM as an Image Using VM Import/Export](vmimport-image-import.md)\.

**Limitations**
+ VM Import/Export supports importing Windows instances into most instance types\. Linux instances can be imported into the following instance types:
  + General purpose: `t2.micro` \| `t2.small` \| `t2.medium` \| `m3.medium` \| `m3.large` \| `m3.xlarge` \| `m3.2xlarge`
  + Compute optimized: `c3.large` \| `c3.xlarge` \| `c3.2xlarge` \| `c3.4xlarge` \| `c3.8xlarge` \| `cc1.4xlarge` \| `cc2.8xlarge`
  + Memory optimized: `r3.large` \| `r3.xlarge` \| `r3.2xlarge` \| `r3.4xlarge` \| `r3.8xlarge` \| `cr1.8xlarge`
  + Storage optimized: `i2.xlarge` \| `i2.2xlarge` \| `i2.4xlarge` \| `i2.8xlarge` \| `hi1.4xlarge` \| `hi1.8xlarge`
+ The `ImportInstance` and `ImportVolume` API actions are supported only in the following Regions and will not be supported in any additional Regions\.
  + North America: us\-east\-1 \| us\-west\-1 \| us\-west\-2 \| us\-east\-2 \| ca\-central\-1 \| us\-gov\-west\-1
  + South America: sa\-east\-1
  + Europe/Middle East/Africa: eu\-west\-1 \| eu\-central\-1
  + Asia Pacific: ap\-southeast\-1 \| ap\-northeast\-1 \| ap\-southeast\-2 \| ap\-northeast\-2 \| ap\-south\-1 \| cn\-north\-1

**To import your VM to Amazon EC2 as an instance using the legacy Amazon EC2 CLI**  
You must export the VM from your virtualization environment and then import it to Amazon EC2 using the Amazon EC2 CLI, which is deprecated\. For more information, see [Importing a VM to Amazon EC2](https://awsdocs.s3.amazonaws.com/EC2/ec2-clt.pdf#UsingVirtualMachinesinAmazonEC2) in the *Amazon EC2 Command Line Reference*\.