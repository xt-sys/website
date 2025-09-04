+++
title = 'Dynamic Multi-Level Paging and Experimental LA57 Support'
author = 'Aiken Harris'
date = '2025-09-04T19:21:37+01:00'
+++
We have reached an important milestone in the development of ExectOS. While this is not a release announcement, we are
excited to share significant progress on the virtual memory subsystem that lays the foundation for the future kernel
memory manager.

Although the kernel does not yet have a fully functional memory manager, the latest work ensures that when we begin its
implementation, it will seamlessly support all paging levels from the very start. With proper handling of PML2, PML3,
PML4, and PML5 already integrated into the bootloader and partially into the kernel, we have created a highly flexible
and future-proof paging framework.

## Dynamic Multi-Level Paging
Unlike most operating systems, which require separate kernel builds or compile-time configuration to handle different
paging models, ExectOS takes a unique approach. Instead of choosing a single paging mode at build time, the kernel
selects the correct strategy **dynamically at runtime**.

To achieve this, we have introduced the concept of XPA — eXtended Physical Addressing. This is an internal term used to
unify two different hardware features under a single abstraction:
 * On i686, XPA refers to PAE (Physical Address Extension), which enables PML3.
 * On AMD64, XPA refers to LA57 (5-level paging), which enables PML5.

The kernel automatically detects whether the CPU supports PAE or LA57 and chooses the appropriate paging strategy. If
the user wants to disable extended paging modes, this can be done with a single boot parameter: **NOXPA**. Without this
abstraction, we would have needed two separate options (**NOPAE** and **NOLA57**), complicating both code and
configuration.

## The Strategy-Based Design
The new paging subsystem uses the Strategy design pattern to provide both flexibility and maintainability. Instead of
hardcoding assumptions about paging levels, we now have two cooperating structures:
 * **MMPAGEMAP_INFO**: Stores static information about the active paging mode, such as base virtual addresses, offsets
   for PTE/PDE/PPE/PXE/P5E levels, and flags describing whether XPA is enabled.
 * **MMPAGEMAP_ROUTINES**: Holds function pointers for all paging-related operations, such as validating PTEs,
   allocating new page tables, or changing caching attributes.

At runtime, ExectOS binds the appropriate function implementations to the MMPAGEMAP_ROUTINES structure based on the
selected paging level, while the MMPAGEMAP_INFO structure provides the necessary constants and offsets for correct
address translation.

This design allows us to maintain a single unified kernel image capable of running on CPUs with very different paging
capabilities. More importantly, it sets the stage for a future memory manager that will "just work" with any supported
paging level without requiring additional patches, recompilation, or feature flags.

## Performance Considerations
You might expect that introducing function pointers and dynamic dispatch could hurt performance, but in practice, the
overhead is negligible. The Strategy pattern affects only the rare moments when memory is being mapped or unmapped -
operations that are infrequent compared to ordinary memory access.

Once a virtual address is mapped, the CPU uses the hardware-managed Translation Lookaside Buffer (TLB) to cache
virtual-to-physical translations. Accessing mapped memory does not require any extra indirection through our structures.
The cost of an additional function pointer lookup occurs only during the setup phase of a mapping, which involves much
more expensive operations anyway - flushing TLB entries, updating PTEs, and invalidating caches. In other words, the
small dispatch cost is completely overshadowed by the inherent complexity of memory mapping itself.

In summary, the Strategy-based design gives us dynamic flexibility without sacrificing performance, even on systems with
tight CPU cycle budgets.

## Experimental LA57 Support
For AMD64, we have added experimental support for 5-level paging (PML5), which expands the virtual address space from
48-bits to 57-bits. To enable LA57, we implemented a small trampoline that temporarily switches the CPU into 32-bit
compatibility mode, sets the CR4.LA57 bit, and then returns to long mode with the new PML5 hierarchy active.

Because we currently lack access to real hardware supporting LA57, this functionality has been tested exclusively in
QEMU. While the implementation is complete and verified in a virtualized environment, we consider it experimental until
we can validate it on physical systems.

During development, we also addressed an issue related to the calculation of virtual-to-physical translation offsets
when working in 5-level paging mode. The constants defining the base virtual addresses for the page-table self-mapping
region were previously chosen in a way that, under certain conditions, could lead to address calculation overflows when
deriving the location of a Page Table Entry (PTE) from a given virtual address. We have corrected these base address
definitions, ensuring that the entire PML5 hierarchy is now computed safely and consistently across all supported paging
modes.

## Looking Ahead
With the new foundation in place, the next major milestone will be implementing the kernel memory manager. Alongside
this, we will continue refining the existing code through bug fixes, optimizations, and refactoring to ensure long-term
stability and maintainability.
