+++
title = 'Development Update'
author = 'Aiken Harris'
date = '2026-02-14T11:17:38+01:00'
+++
It has been several months since our last update. During this time, development of ExectOS has continued steadily,
although much of the work has taken place deep within the kernel and boot process. While the original goal of
delivering a fully functional memory manager has not yet been achieved, we believe this is a good moment to inform
the community about the progress made and the challenges addressed along the way.
<!--more-->

## Focus Area: Memory Manager
Since the previous update, development has been focused exclusively on the kernel memory manager. This work required
substantial changes not only within the memory subsystem itself but also in the bootloader, where several critical
issues were discovered.

## Bootloader Issues and Fixes
During integration work, it became clear that our custom bootloader still contained serious flaws that directly
impacted memory manager development:
 * **XTLDR and XTOSKRNL incompatibility**: The bootloader and the kernel used different memory mapping strategies
   across architectures. This mismatch resulted in inconsistent virtual memory layouts and prevented reliable
   initialization of the memory manager. The mapping strategies have now been unified to ensure predictable behavior.
 * **Incorrect identity mapping of UEFI-reserved memory**:  The bootloader was identity-mapping memory regions reserved
   by UEFI. This was a design error, as it unnecessarily consumed limited virtual address space. The mapping logic has
   been corrected to avoid reserving virtual space for firmware-managed regions.
 * **Critical mapping construction bug**:  A severe issue in the bootloader's memory mapping builder caused overlapping
   virtual memory regions. This made correct construction of the PFN (Page Frame Number) database impossible. The
   mapping generation logic has been rewritten to guarantee non-overlapping, deterministic memory regions.

### PAE Handling Fix (Bug #23)
We also resolved bug #23, which affected PAE support on the i686 platform. The root cause was improper handling of
memory descriptors above the 32-bit boundary. The fix ensures that all valid memory regions are correctly preserved and
passed to the kernel, allowing full utilization of available RAM under PAE.

## Progress in the Memory Manager
Beyond bootloader fixes, significant progress has been made within the memory manager itself.

### Dynamic Memory Layout Construction
The kernel now dynamically builds its memory layout based on the total amount of detected physical RAM. This ensures
that core memory regions, such as: PagedPool, NonPagedPool, or system structures and internal mappings are assigned
clearly defined and non-overlapping virtual address ranges. This dynamic approach improves scalability and makes the
layout adaptable to systems with varying memory configurations.

### PFN Database Initialization
We are now successfully constructing the PFN database and mapping all memory regions required for the Memory Manager's
operation. This was a key milestone, as many higher-level memory operations depend on a correctly initialized PFN layer.

### Stack Allocation and Deallocation
We have implemented internal routines for allocating and freeing memory intended for kernel stacks. These mechanisms
allow:
 * Allocation of stacks with arbitrary sizes depending on requirements,
 * Proper tracking and release of stack memory
This is a prerequisite for thread management. As a result, we can now begin work on kernel thread support. In the near
future, we plan to introduce functionality for dynamically growing existing stacks.

## Current Work
At present, development is focused on implementing general-purpose allocation and deallocation routines for arbitrary
address pools, specifically: PagedPool and NonPagedPool. These allocators are essential for enabling further kernel
subsystems.
One of the next major items on our roadmap is SMT support. We intend to introduce it as early as possible to reduce
issues related to resource locking and race conditions. Bringing up symmetric multithreading early should help expose
and eliminate concurrency-related bugs before additional kernel complexity is introduced.

## Summary
Although the fully functional memory manager is not yet complete, substantial foundational work has been finished:
 * Critical bootloader memory mapping bugs resolved
 * Proper PAE handling restored
 * Dynamic kernel memory layout construction implemented
 * PFN database successfully built
 * Kernel stack allocation mechanisms introduced

The project is now in a more stable and architecturally consistent state than it was before. The full and detailed
list of changes can be found in the **memmgr** branch. Further updates will follow as we continue stabilizing the
allocator infrastructure.
