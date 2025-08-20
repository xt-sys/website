+++
title = 'Major Bugfixing Milestone Unlocks New Development Path for ExectOS'
author = 'Aiken Harris'
date = '2025-08-20T11:13:37+02:00'
+++
Over the past month, since the release of XTbuild 3.0.1, development efforts on ExectOS have focused primarily on resolving
critical bugs that were blocking further progress on the project. While feature development temporarily took a back seat,
the work done during this period has been substantial - especially considering that it was carried out by a single developer.

These fixes were essential: as new features were being implemented, they frequently triggered unexpected Page Faults in
regions of the system where memory behavior should have been stable. After extensive debugging, the root causes were traced
to issues such as incorrect stack alignment and faulty memory mapping. Thankfully, many of these problems have now been
resolved, paving the way for continued feature development.

## Key Fixes and Improvements
Among the most important technical fixes completed in the past month:
* **Removed compiler library dependencies**, particularly the reliance on memset(), ensuring full control over runtime behavior.
* **Corrected stack initialization**, taking into account that the stack grows downward and should be initialized to its logical
  end.
* **Proper stack alignment**, preventing subtle memory access issues.
* **Enhanced COM port support**, improving serial communication stability.
  * Resolved a race condition in HlComPortReadLsr by making RingIndicator part of the CPPORT structure instead of using a
    shared static variable.
* **Fixed TSS descriptor limit** to comply with the Intel architecture specification.
* **Improved XTLDR behavior**:
  * Automatic booting of the default system after a timeout now works reliably.
  * Proper highlighting of the default boot menu entry.
  * Refined volume handling in XTLDR, allowing for correct detection of CD/DVD drives.
  * Fixed bootloader logic in XTOS_O and CHAINLDR modules to ensure the system is booted from the correct volume instead of
    always defaulting to the ESP partition.
* **Improved build pipeline**:
  * Automated builds for both i686 and AMD64.
  * Separate outputs for debug and release builds.
* **Explicit paging activation on AMD64**.
* **Correct initialization of memory-related structures** through zero-filling before usage.
* **Improved CPUID handling**.
* **Replaced XTSTATUS with EFI_STATUS** in the bootloader, aligning with EFI standards.
* **Memory mapping and PML handling fixes**:
  * Fixed a PDE conflict on i686 systems without PAE when mapping last 4MB

## Additional Enhancements
In parallel with the bugfixes, several usability and maintainability improvements were introduced:
* Added a build configuration script for compiling the project on Windows.
* Replaced hardcoded values with constants and definitions for better maintainability.
* Implemented menu scrolling support in the XTLDR bootloader.
* Changed build output directories to allow separate debug and release folders.
* Implemented kernel parameter support in the bootloader.
* Full support for PML2 and PML3 in the bootloader, enabling system boot with or without PAE support.
* Introduced the *NOXPA* parameter to disable PAE (i686) and LA57 (AMD64) as needed.
* Automatic PML level detection:
  * The bootloader now queries CPUID for PAE or LA57 support depending on architecture.
  * If the user hasn't explicitly disabled XPA, the bootloader enables PAE when available.
  * LA57 remains unsupported for now, but groundwork has been laid.

## One Month, One Developer, One Giant Leap
Given the size and complexity of the fixes, this is a remarkable milestone for just one month of development. More
importantly, resolving these core issues has unblocked the project’s forward momentum. With stability greatly improved,
the path is now clear to resume development of advanced features without being derailed by low-level system bugs.

Stay tuned - the next phase of ExectOS development promises to be an exciting one.
