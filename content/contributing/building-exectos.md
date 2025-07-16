+++
title = 'Building ExectOS'
date = 2024-06-18T16:37:24+02:00
+++
ExectOS must be compiled with the XTChain toolchain. Make sure you have already [downloaded](/download) and installed
XTchain and [set up the environment](/contributing/setting-up-xtchain) before proceeding. Once this is done and the
source code is ready, open a terminal and launch the XTChain Build Environment.
```
xtchain
```
Next, set your target architecture and build type. You can choose between:
 * Architecture: i686 (32-bit) or amd64 (64-bit)
 * Build type: DEBUG (for development and debugging) or RELEASE (for production)

Use the following commands:
```
charch [i686|amd64]
chbuild [DEBUG|RELEASE]
```
Now, configure the project:
 * On Linux, run:
```
./configure.sh
```
* On Windows, run:
```
.\configure.ps1
```
Once configuration is complete, navigate to the build directory and compile the source:
```
cd build
xbuild
```
After the build completes, the compiled binaries will be located in the `build/output/binaries` directory. Additionally,
depending on the selected build type, you may also find debug symbols in PDB format inside the `build/output/symbols`
folder. Precompiled libraries intended for linking other software with XTDK are available in the `build/output/library`
directory.

## Creating Qemu Disk Image
If you want to build a bootable disk image for use with QEMU, you can do so with the following command:
```
xbuild diskimg
```
This command generates a virtual disk image that can be directly booted in a QEMU virtual machine. You can also convert
this image into other formats compatible with alternative hypervisors such as VirtualBox or VMware.

## Testing with Qemu
Once ExectOS has been successfully compiled, you can test it in a virtual environment. The recommended approach is to use
QEMU, which supports both hardware virtualization (via KVM) and software emulation (via TCG).

After successfully compiling ExectOS, you can test it using QEMU with either hardware virtualization (KVM) or software
emulation (TCG). To start QEMU with KVM, use the testkvm target, and for TCG emulation, use the testtcg target. These
targets are provided for convenience and can be executed directly from the build directory. For example, run
`xbuild testkvm` to launch QEMU with hardware virtualization, or `xbuild testtcg` to use software emulation:
```
xbuild testkvm  # For KVM hardware virtualization
```
or
```
xbuild testtcg  # For TCG software emulation
```
These commands will automatically configure QEMU with the appropriate settings and start the virtual machine. Keep in mind
that QEMU is not distributed with XTchain and must be installed manually.

In addition to QEMU, you can also test ExectOS using Bochs, an IA-32 (x86) PC emulator that offers a different emulation
environment. To launch Bochs with the ExectOS disk image, run the following command:
```
xbuild bochsvm
```
This will start the Bochs emulator with ExectOS loaded, allowing you to test the system in a context that may offer
different debugging capabilities compared to QEMU. Like QEMU, Bochs is not bundled with XTchain and needs to be installed
separately.

The performance and debugging experience will differ depending on which emulator you use. KVM requires a host system with
hardware virtualization support and offers the best performance. TCG is a pure software-based emulator and works even on
systems without virtualization capabilities, albeit at slower speeds. Bochs, while slower than both, provides a rich set of
debugging features which can be especially helpful when troubleshooting complex or low-level issues. Alternatively, you may
use other hypervisors like VMware or VirtualBox by manually configuring them to boot from the generated disk image.
