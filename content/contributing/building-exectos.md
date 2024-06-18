+++
title = 'Building ExectOS'
date = 2024-06-18T16:37:24+02:00
+++
ExectOS must be compiled with the XTChain toolchain. Make sure you have already [downloaded](/download) and installed it.
Once this is done and the source code is ready, open a terminal and launch the XTChain Build Environment.
```
xtchain
```
Then, set your target build architecture and the build type and configure the project. Choose `i686` or `amd64` depending
on the architecture you wish to target. The build type can be either `DEBUG` for debugging purposes or `RELEASE` for
a production build.
```
charch [i686|amd64]
chbuild [DEBUG|RELEASE]
./configure.sh
```
After the sources are configured, navigate to the build directory and compile the source code.
```
cd build
xbuild
```
Afterwards, you will find the binaries in `build/output/binaries` directory. Apart from that, depending on build type, you
can also find a debug symbols in PDB format in `build/output/symbols` folder and precompiled libraries that can be used to
link other software using XTDK in `build/output/library`

## Creating Qemu Disk Image
If you want to build a disk image that is ready to use with Qemu, you can use the following command:
```
xbuild diskimg
```
This will create a disk image that you can boot within a Qemu Virtual Machine, or convert to other format for use within
other hypervisor, like VirtualBox.

## Testing with Qemu
After successfully compiling ExectOS, you can test it using Qemu with either hardware virtualization (KVM) or software
emulation (TCG). To start Qemu with KVM, use the `testkvm` target, and for TCG emulation, use the `testtcg` target. These
targets are provided for convenience and you can run these commands from your build directory.
```
xbuild testkvm  # For KVM hardware virtualization
```
or
```
xbuild testtcg  # For TCG software emulation
```
These commands will automatically configure Qemu with the appropriate settings for ExectOS and start the virtual machine.
In addition to Qemu, you can also use Bochs for emulation. Bochs is an IA-32 (x86) PC emulator that provides a different
emulation environment. To launch Bochs with the ExectOS image, run the following command:
```
xbuild bochsvm
```
This will start the Bochs emulator with ExectOS loaded, allowing you to test the system in an environment that may offer
different debugging capabilities compared to Qemu.

Remember that the performance and debugging features will vary depending on whether you use KVM, TCG, or Bochs. KVM
requires that your host machine supports hardware virtualization, while TCG is a pure software emulator which works on
systems without hardware virtualization. Bochs provides a rich set of debugging features, which can be useful when
troubleshooting complex issues.
