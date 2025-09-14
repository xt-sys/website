+++
title = 'Deep Dive into Recent Bug Fixes'
author = 'Aiken Harris'
date = '2025-09-14T11:51:17+01:00'
+++
August has been an intense and productive month for the ExectOS project. When many thought development had slowed down,
we managed to push forward with progress - a clear sign that ExectOS is still actively evolving. Alongside implementing
Dynamic Multi-Level Paging and adding experimental LA57 support, we also spent a considerable amount of time addressing
a wide range of bugs, improving stability and compatibility across the entire system.

In one of our recent updates, we published a full list of bug fixes included in the latest builds. This time we wanted to
share some of the more interesting technical findings that came out during the development process. While many fixes were
straightforward, others required a deep dive into low-level kernel internals, bootloader behavior, and hardware-specific
quirks. In this article, we have selected several cases that stood out due to their complexity or the insights they
provided. Our goal is to give you a closer look behind the scenes and highlight the complex engineering challenges we
encounter.


### Correcting String Parameter Types: From CONST PWCHAR to PCWSTR
During a recent code review, we identified several inconsistencies in function definitions where parameters or return
types used **CONST PWCHAR** instead of **PCWSTR**. At first glance, the two may seem equivalent, but the distinction is
subtle and significant. The type **PCWSTR** is defined as **const WCHAR***, meaning it represents a pointer to immutable
data - the function promises not to modify the contents of the string it receives. In contrast, **PWCHAR** is defined as
**WCHAR* **, a pointer to mutable data, and when combined with the const qualifier as **CONST PWCHAR**, the compiler
interprets it as **WCHAR * const**. This means the pointer itself is constant, but the data it points to can still be
modified. In other words, the original declarations implied that the function would not change the pointer's address but
made no guarantees about preserving the actual string content. Since our intention was to ensure that the functions never
alter the provided strings, we corrected the signatures to use **PCWSTR**. This change improves type safety, prevents
accidental data modifications, and better communicates the intended contract of these APIs.


### Fixing a Stack Corruption Bug in BlEnumerateBlockDevices
While investigating random crashes in the bootloader, we discovered a subtle but critical bug in the
**BlEnumerateBlockDevices()** function, specifically within a **BlDebugPrint()** call responsible for logging detected
hard disk partitions. The issue stemmed from using the wrong format specifier for printing the partition size. The code
was using **%u** to display a value computed as **HDPath->PartitionSize * Media->BlockSize**, which is stored in a 64-bit
**UINT64** variable. On 32-bit UEFI systems, **%u** expects a **32-bit unsigned int**, causing only the lower 32 bits of
the value to be read while the upper 32 bits remained on the stack. As a result, all subsequent arguments were
misaligned, leading **BlDebugPrint()** to interpret invalid data as a string pointer for the **%S** specifier. In
practice, this caused corrupted output, random memory reads, and eventually a protection fault that froze the system. The
fix was straightforward but essential: we replaced **%u** with **%llu** to correctly print the 64-bit value, preventing
stack corruption and restoring XTLDR stability.


### Eliminating Screen Flicker with Smarter Boot Menu Redrawing
Previously, the boot menu suffered from a performance issue where every keypress - even a simple UP/DOWN arrow -
triggered a complete redraw of the entire menu. This approach was inefficient and caused noticeable flickering. To fix
this, we introduced a smarter redrawing mechanism controlled by two new flags: **RedrawBootMenu** and **RedrawEntries**.
The **RedrawBootMenu** flag indicates when the entire menu, including the header and footer, must be redrawn - for
example, after displaying the help screen or an error message. The **RedrawEntries** flag, on the other hand, signals
when the list of visible entries needs refreshing, such as when the selection moves beyond the currently displayed area.
For normal navigation, the optimized arrow key handling now tracks the previously highlighted item. When the new
selection remains within the visible range, only two drawing operations are performed: the old item is redrawn without
highlighting, and the new one is drawn with highlighting. The rest of the list remains untouched, eliminating unnecessary
updates. This change completely removes flickering, significantly reduces CPU overhead, and results in smooth, menu
navigation.


### Restoring Build Compatibility by Replacing asm with \_\_asm\_\_
After upgrading CMake from 4.0.3 to 4.1.0, the project became uncompilable when building with clang-cl under the C23
standard. The root cause was the stricter enforcement of language compliance introduced in the new CMake release.
Previously, CMake 4.0.3 did not explicitly pass a C standard flag, which allowed clang-cl to operate in a GNU-compatible
mode where the shorthand asm keyword - provided as a GNU extension - was accepted. However, starting with CMake 4.1.0,
the build system began explicitly adding the **-clang:-std=c23** flag, forcing the compiler into strict ISO C23 mode and
disabling GNU-specific extensions. As a result, any occurrence of asm caused a compilation error:

```error: use of undeclared identifier 'asm'```

To resolve this, we replaced all instances of **asm** with the standard-compliant **\_\_asm\_\_** keyword. Unlike **asm**,
which is a GCC-specific convenience, **\_\_asm\_\_** is a reserved identifier explicitly recognized by both GCC and Clang,
even in strict standards mode. This change ensures consistent compatibility across compilers and aligns the codebase with
the C23 standard.


### Fixing a Race Condition in HlComPortReadLsr Caused by a Shared Static Variable
We discovered a critical race condition and state management issue in the HlComPortReadLsr function, which handles
reading from serial (COM) ports. The root cause was the use of a local static variable:

``STATIC UCHAR RingFlag;``

In C, declaring a local variable as static means it is allocated only once and retains its value across all future calls
to the function. However, this also means there is only a single instance of RingFlag, shared by every call to
**HlComPortReadLsr()** - regardless of which COM port (PCPPORT instance) is being accessed. This design flaw introduced
two major problems - State Corruption. The function incorrectly allowed the state of one port to "leak" into another. If
**HlComPortReadLsr()** was called for COM1 and set **RingFlag** to a specific value, a subsequent call for COM2 would
reuse that value, leading to incorrect logic and inconsistent results. The solution was to remove the shared static
variable and make **RingFlag** instance-specific, ensuring each port operates on its own state.


### Fixing Framebuffer Rendering by Using Correct Pitch and Bytes-Per-Pixel Calculations
We fixed a bug in the framebuffer driver that caused severe graphical glitches on many real devices. The issue stemmed
from incorrect assumptions about how pixel data is laid out in video memory. The original implementation in
**xtoskrnl/hl/fbdev.c** made two major mistakes when calculating pixel addresses:
 * **Ignoring the Pitch (Stride):** The code assumed that moving from one scanline to the next could be done by adding
   **screen_width * bytes_per_pixel**. It used **HlpFrameBufferData.PixelsPerScanLine** - the number of visible pixels
   per line - to perform the calculation. However, modern graphics hardware often aligns each scanline to specific memory
   boundaries (e.g., 16 bytes), which means the actual distance between the start of consecutive lines, known as the
   **Pitch (or stride)**, can be larger than **width * bytes_per_pixel**. By ignoring Pitch, the code miscalculated the
   start of each line, causing images to appear skewed, torn, or completely distorted.
 * **Hardcoding Pixel Size:** The original driver assumed that each pixel was always 4 bytes (32-bit color), using
   formulas like:
   ``FrameBufferIndex = 4 * PositionY + 4 * PositionX;``
   This made the driver incompatible with display modes using 24-bit or 16-bit color depths.

We have addressed these issues by introducing a hardware-correct approach. We have added BytesPerPixel to the framebuffer
data structure. It's value is calculated once during initialization. This eliminates all hardcoded assumptions about pixel
size.

Additionally, all drawing functions were updated to use the proper formula for calculating pixel address. This fix ensures
correct rendering across all supported color depths and hardware configurations, eliminates graphical artifacts, and makes
the framebuffer driver fully compliant with modern graphics standards.
