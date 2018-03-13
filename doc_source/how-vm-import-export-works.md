# How VM Import/Export Works<a name="how-vm-import-export-works"></a>

To use your VM in Amazon EC2, you must first export it from the virtualization environment, and then import it into Amazon EC2 as either an Amazon Machine Image \(AMI\) or an instance\.

## Benefits<a name="vmimport-benefits"></a>

You can use VM Import/Export to migrate applications and workloads, copy your VM image catalog, or create a disaster recovery repository for VM images\.

+ **Migrate existing applications and workloads to Amazon EC2**—When you migrate your VM\-based applications and workloads to Amazon EC2, you preserve their software and configuration settings\. When you create an AMI from your VM, you can run multiple instances based on the same imported VM\. You can also use the AMI to replicate your applications and workloads around the world using AMI copy\. For more information, see [Copying an AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

+ **Import your VM image catalog to Amazon EC2**— If you maintain a catalog of approved VM images, you can copy your image catalog to Amazon EC2 and create AMIs from the imported images\. You can import your existing software, including products that you have installed such as anti\-virus software, intrusion detection systems, and so on, along with your VM images\. You can use the AMIs you create as your Amazon EC2 image catalog\.

+ **Create a disaster recovery repository for VM images**—You can import your local VM images into Amazon EC2 for backup and disaster recovery purposes\. You can import your VMs and store them as AMIs\. The AMIs you create will be ready to launch in Amazon EC2 when you need them\. If your local environment suffers an event, you can quickly launch your instances to preserve business continuity while simultaneously exporting them to rebuild your local infrastructure\.

## Differences Between Image Import and Instance Import<a name="vmimport-differences"></a>

The following table summarizes the key differences between image import and instance import\.


| Characteristic | Image import | Instance import | 
| --- | --- | --- | 
|  CLI support  |  AWS CLI  |  Amazon EC2 CLI  | 
|  Supported formats for import  |  OVA, VHD, VHDX, VMDK, raw  |  VHD, VMDK, raw  | 
|  Multi\-disk support  |  ✔  |   | 
|  Windows BYOL support  |  ✔  |   | 

## Image Import<a name="image-import"></a>

First, you prepare your virtual machine for export, and then export it using one of the supported formats\. Next, you start the import task, which uploads the VM image to Amazon S3 and creates an AMI\. After the import task is complete, you can launch an instance from the AMI\. If you want, you can copy the AMI to other regions so that you can launch instances in those regions\.

The following diagram shows the process of exporting a VM from your virtualization environment to Amazon EC2 as an AMI\.

![\[VM Import/Export image import\]](http://docs.aws.amazon.com/vm-import/latest/userguide/images/vmimport-export-architecture-import-image.png)

## Instance Import<a name="instance-import"></a>

First, you prepare your virtual machine for export, and then export it using one of the supported formats\. Next, you start the import task, which uploads the VM image to Amazon S3 and launches it as a stopped instance\. After the import task is complete, you can create an AMI from the instance\. If you want, you can copy the AMI to other regions so that you can launch instances in those regions\. You can also export a previously imported instance to your virtualization environment\.

The following diagram shows the process of exporting a VM from your virtualization environment to Amazon EC2 as an instance\.

![\[VM Import/Export instance import\]](http://docs.aws.amazon.com/vm-import/latest/userguide/images/vmimport-export-architecture-ami-copy.png)