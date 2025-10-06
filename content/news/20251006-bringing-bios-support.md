+++
title = 'ExectOS Expands Beyond UEFI: Bringing BIOS Support'
author = 'Aiken Harris'
date = '2025-10-06T10:36:18+01:00'
+++
When we first started building ExectOS, we envisioned it as an operating system designed exclusively for UEFI-based
systems. Over time, however, we realized that this requirement was an artificial limitation. Today, we are excited to
announce that we have begun taking concrete steps toward supporting traditional PC/AT hardware with classic
BIOS - broadening the reach of ExectOS to a much wider range of systems.
<!--more-->

## First Steps Toward BIOS Compatibility
We have already implemented an MBR boot sector and updated our CMake configuration to support BIOS-based emulation. As
of now, ExectOS can be launched in several new testing configurations:
 * **bochsvm**: Bochs with BIOS
 * **testkvm/testtcg**: QEMU with classic BIOS (with KVM/WHPX acceleration or TCG without acceleration)
 * **testefikvm/testefitcg**: QEMU with modern UEFI (with or without acceleration), as before

This means developers can now experiment with ExectOS under both UEFI and BIOS environments using familiar tools like
Bochs and QEMU.

## A Unified Boot Partition Model
To simplify BIOS support, we are introducing a dedicated BOOT partition - similar to the recommended practice on
Unix-like systems and analogous to the ESP (EFI System Partition) used by UEFI. Our bootloader will exclusively support
FAT32 on this partition. It will house the bootloader itself, its configuration, and all modules. In other words, we are
effectively bringing the concept of the ESP from the UEFI world into the BIOS realm.

## What's Next?
Full BIOS support is still some way off. The current MBR implementation and CMake adjustments are just the first step. We
still need to:
* Implement a VBR (Volume Boot Record) capable of locating and loading files from disk
* Develop a preloader to prepare the environment for the main bootloader (enabling paging, long mode, etc.)
* Significantly refactor XTLDR, to eliminate assumptions about UEFI, such as memory allocation APIs
* Ensure all modules are environment-agnostic, running seamlessly on both BIOS and UEFI systems
* Move PE/COFF file handling from the module level into the main XTLDR code

While there is still a long road ahead, this change opens up new possibilities for ExectOS. By removing the UEFI-only
limitation, we are making the system more flexible and accessible.
