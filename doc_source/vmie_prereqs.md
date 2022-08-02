# VM Import/Export Requirements<a name="vmie_prereqs"></a>

Before attempting to import a VM, you may need to perform tasks such as preparing your AWS environment by creating a service account with appropriate permissions, and preparing your locally hosted VM so that is accessible once it is imported into AWS\. Review each of these requirements and take action as needed\.

**Contents**
+ [System requirements](prerequisites.md)
  + [Image formats](prerequisites.md#vmimport-image-formats)
  + [Operating systems](prerequisites.md#vmimport-operating-systems)
    + [Linux/Unix OS \(64\-bit only\)](prerequisites.md#vmimport-operating-systems-linux)
    + [Windows OS](prerequisites.md#vmimport-operating-systems-windows)
  + [Boot modes](prerequisites.md#vmimport-boot-modes)
  + [Volume types and file systems](prerequisites.md#vmimport-volume-types)
+ [Licensing options](licensing.md)
  + [Licensing for Linux](licensing.md#linux)
  + [Licensing for Windows](licensing.md#windows)
+ [Limitations](limitations-image.md)
+ [Required permissions for VM Import/Export](required-permissions.md)
  + [Required permissions for IAM users](required-permissions.md#iam-permissions-image)
  + [Required service role](required-permissions.md#vmimport-role)
+ [Required configuration for VM export](prepare-vm-image.md)
+ [Programmatic modifications to VMs](import-modify-vm.md)