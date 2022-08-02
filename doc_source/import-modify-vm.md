# Programmatic modifications to VMs<a name="import-modify-vm"></a>

When importing a VM using the `ImportImage` API, AWS modifies the file system to make the imported VM accessible to the customer\. The following actions may occur:
+ \[Linux\] Installing Citrix PV drivers either directly in OS or modify initrd/initramfs to contain them\.
+ \[Linux\] Modifying network scripts to replace static IPs with dynamic IPs\.
+ \[Linux\] Modifying `/etc/fstab`, commenting out invalid entries and replacing device names with UUIDs\. If no matching UUID can be found for a device, the `nofail` option is added to the device description\. You must correct the device naming and remove `nofail` after import\. As a best practice when preparing your VMs for import, we recommend that you specify your VM disk devices by UUID rather than device name\.

  Entries in `/etc/fstab` that contain non\-standard file system types \(cifs, smbfs, vboxsf, sshfs, etc\.\) are disabled\.
+ \[Linux\] Modifying grub bootloader settings such as the default entry and timeout\.
+ \[Windows\] Modifying registry settings to make the VM bootable\.
+ \[Both\] For parity with images provided by AWS, the AWS Systems Manager client is installed on the VM\.

When writing a modified file, AWS retains the original file at the same location under a new name\.