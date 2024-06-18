+++
title = 'Installation Guide'
date = 2024-06-17T16:41:29+02:00
+++
ExectOS is in early development phase and thus it is NOT suitable for daily use, especially on real hardware. It is
recommended to run it in a virtual machine like Qemu or VirtualBox. Otherwise, you act at your own risk!

### Running in Qemu
Running ExectOS in Qemu is straight-forward, as our build pipeline produces a disk image, that is ready to use in Qemu.
Once you download it and extract, you can use below commands to run the virtual machine, depending on the target CPU
architecture of the image. For x86_64 use:
```
qemu-system-x86_64 -cpu host -enable-kvm -m 2G -hda disk-amd64.img -serial stdio \
                   -drive file=sdk/firmware/ovmf_code_amd64.fd,if=pflash,format=raw,unit=0,readonly=on \
                   -drive file=sdk/firmware/ovmf_vars_amd64.fd,if=pflash,format=raw,unit=1

```
And for i686 use:
```
qemu-system-i386 -cpu host -enable-kvm -m 1G -hda disk-i686.img -serial stdio \
                   -drive file=sdk/firmware/ovmf_code_i686.fd,if=pflash,format=raw,unit=0,readonly=on \
                   -drive file=sdk/firmware/ovmf_vars_i686.fd,if=pflash,format=raw,unit=1

```
Above commands require an UEFI firmware to be available. It can be found in the
[ExectOS GIT repository](https://git.codingworkshop.eu.org/xt-sys/exectos/src/branch/master/sdk/firmware).

### Running in VirtualBox
VirtualBox is commonly used for running the system in an isolated environment. In this case, the same disk image as
for Qemu can be used. However, it needs to be converted to QCOW2 format, that is supported by VirtualBox. This can be
done with the following command:
```
qemu-img convert -O qcow2 disk.img disk.qcow2
```
Once, the disk image is converted, you can create a new virtual machine. As an Operating System, you can choose either
32-bit or 64-bit Windows&reg; 10, depending on ExectOS disk image architecture. Just make sure, that EFI support is
enabled.

### Running on Bare Metal
It is recommended to not install, nor run ExectOS on a real hardware. Otherwise, installation should be
straight-forward and self descriptive:
 * Download a binary package or build XTOS yourself.
 * Prepare a pendrive or USB stick. It should have at least first partition formatted as FAT32.
 * Copy all binaries to USB drive, keeping the existing directory structure. Contents of the archive, or contents of
   `build/output/binaries` is the root of your drive. Do NOT paste them into any subdirectory.
 * Reboot the computer.
 * Enter Firmware Setup or Boot Selection menu and choose your USB drive.
 * Viola, enjoy!

In case of any problems, take a photo of your screen and contact with ExectOS developers.
