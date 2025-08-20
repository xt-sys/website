+++
title = 'Download'
date = 2024-06-16T22:35:11+02:00
+++
### ExectOS
ExectOS currently supports two architectures: i686 and AMD64. To get started, you can either download the appropriate
binaries for your platform, or test-drive ExectOS using the QEMU emulator by downloading one of the available disk images.
All available files are listed on [this page](https://artifacts.codingworkshop.eu.org/ExectOS/?C=M&O=D). Each filename
includes the build date, commit hash, architecture, and build type (debug or release), as well as the artifact type.
For example:

* ExectOS-20250730-61d5e36a4e-i686-release-bin.tar.gz - Contains binary files that can be copied to a USB disk and booted
  on bare metal hardware.
* ExectOS-20250730-61d5e36a4e-i686-release-lib.tar.gz - Includes libraries useful for building 3rd party drivers.
* ExectOS-20250730-61d5e36a4e-i686-release-sym.tar.gz - Contains debug symbols (PDB format) for release build.
* ExectOS-20250730-61d5e36a4e-i686-release.img.gz - A compressed QEMU disk image for testing the release version of the
  system.
* ExectOS-20250730-61d5e36a4e-i686-debug-bin.tar.gz - Contains debug version of system binaries, including additional debug
  output.
* ExectOS-20250730-61d5e36a4e-i686-debug-lib.tar.gz - Libraries with debug symbols, suitable for development and testing.
* ExectOS-20250730-61d5e36a4e-i686-debug-sym.tar.gz - Full debug symbols for the debug build, in PDB format.
* ExectOS-20250730-61d5e36a4e-i686-debug.img.gz - A compressed QEMU disk image containing the debug build of ExectOS.

### XTChain
Get the latest prebuilt toolchain, needed to compile the ExectOS Operating System. This includes the Clang compiler, and
other build utilities you need to cross compile the project, including system core (boot loader, kernel and drivers).
The toolchain supports both i686 and AMD64 architectures, as well as ARMv7 and AArch64. Unfortunately, at the moment binary
package is available only for Linux host. To download the XTChain, click [here](https://github.com/xt-sys/xtchain/releases).
