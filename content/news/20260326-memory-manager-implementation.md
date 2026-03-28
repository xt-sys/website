+++
title = 'Memory Manager Implementation'
author = 'Aiken Harris'
date = '2026-03-28T14:37:06+01:00'
+++
The successful integration of the Memory Manager through [PR #24](https://git.codingworkshop.eu.org/xt-sys/exectos/pulls/24)
marks a pivotal moment in the development history of ExectOS, representing the culmination of several months of
intensive low-level engineering and architectural refinement. As a project that aims to implement a brand-new XT
architecture from scratch, the transition from a statically mapped environment to one with dynamic allocation
capabilities is not merely a feature addition but a fundamental prerequisite for the maturation of all subsequent
kernel services. The implementation of the Memory Manager provides the necessary infrastructure for the kernel to
manage system resources with the fluidity required by modern general-purpose operating systems, moving beyond the
limitations of pre-allocated buffers and fixed memory regions.
<!--more-->

## The Architectural Foundation of the Memory Manager
The design of the ExectOS Memory Manager is deeply rooted in the project's XT architecture, which derives its
philosophical underpinnings from the Windows NT model while introducing modern enhancements and original implementation
strategies. In the early stages of the kernel's lifecycle, the lack of a dynamic memory allocator served as a significant
bottleneck, preventing the development of complex executive services such as the Object Manager or the Process and Thread
Manager. The [PR #24](https://git.codingworkshop.eu.org/xt-sys/exectos/pulls/24) introduces a fully functional version of
the Memory Manager that, while currently limited to the non-paged pool, establishes the core logic for physical frame
management and virtual address space orchestration.

The non-paged pool is a critical component of any NT-style kernel. It consists of a range of virtual memory that is
guaranteed to be resident in physical memory at all times. This guarantee is essential for kernel-mode components that
must access memory at high Interrupt Request Levels (RunLevels), where the page fault handler cannot be invoked. By
establishing this pool first, the ExectOS development team has enabled the implementation of other critical kernel
mechanisms that require reliable, non-swappable memory.

### Cross-Architecture Paging Support and the Single-Binary Model
One of the most technically demanding aspects of the ExectOS Memory Manager is its ability to support multiple paging
structures within a single kernel binary. This design choice is a testament to the project's commitment to a unified
Hardware Layer API, which replaces the traditional Hardware Abstraction Layer (HAL) with a more integrated,
hardware-specific approach within the kernel itself.

The Memory Manager is engineered to adapt dynamically to the underlying hardware capabilities detected during the boot
phase. This adaptation includes support for legacy 32-bit paging, expanded 36-bit physical addressing, and the latest
57-bit linear addressing modes available on modern 64-bit processors.

|Architecture | Paging Level | Mechanism             | Address Space Characteristics |
|-------------|--------------|-----------------------|-------------------------------|
| i686        | PML2         | Classic 32-bit Paging | Supports up to 4 GB of linear address space with 4 KB or 4 MB pages
| i686        | PML3         | 3-Level Paging (PAE)  | Extends physical addressing to 64 GB while maintaining a 4 GB virtual space per process
| AMD64       | PML4         | 4-Level Paging        | Provides a 48-bit virtual address space (256 TB) and 52-bit physical addressing
| AMD64       | PML5         | 5-Level Paging (LA57) | Expands virtual address space to 57 bits (128 PB), supporting massive memory configurations

The support for PML5 (LA57) on the AMD64 architecture is particularly notable, as it places ExectOS among a selective
group of kernels capable of leveraging the most advanced memory management features of contemporary CPUs.

## The Challenge of Early Development and the XTLDR Regression
The road to a functional Memory Manager was fraught with significant engineering challenges, many of which originated
outside the kernel itself. The development cycle was extended by several weeks due to unexpected bugs in the XTLDR
(XT Boot Loader) and its interaction with UEFI firmware. These issues highlight the extreme sensitivity of early-stage
kernel development, where the boundary between the loader and the kernel is a frequent source of catastrophic failures.

### Forensic Analysis of Issue 25: The CR3 Corruption Bug
The most critical hurdle encountered was documented in [Issue #25](https://git.codingworkshop.eu.org/xt-sys/exectos/issues/25),
which described a fundamental failure in the memory hand-off protocol between the XTLDR and the kernel. In a modern
EFI-based boot process, the loader is responsible for preparing the initial execution environment for the kernel, which
includes setting up a preliminary set of page tables and enabling paging. The physical address of the top-level Page Map
Level (PML) table is stored in the CR3 register, and the CPU relies on this address for all subsequent memory translations.

The bug in the XTLDR occurred because the loader failed to explicitly reserve the physical frame containing the active
PML table in the memory map passed to the kernel. Consequently, the physical address residing in the CR3 register was
marked as LoaderFree (Type 2). In the XT memory map specification, Type 2 memory is intended for the kernel to reclaim
and use as it sees fit once the loader has finished its task.

When the ExectOS Memory Manager initialized, it scanned the memory map and correctly identified the LoaderFree regions
as available for allocation. As the kernel began to populate its non-paged pool, it eventually requested a new page
for its own internal use. By sheer probability, the allocator selected the frame that was currently being used by the
CPU as the top-level page table.

The catastrophic failure occurred during a call to ```RTL::Memory::ZeroMemory()```. As the kernel attempted to clear
the newly allocated page for use, it effectively wiped the active page table entries. The immediate result was the
destruction of the entire virtual address space. The CPU, suddenly unable to translate the address of the next
instruction, the stack, or the Interrupt Descriptor Table (IDT), was forced into a series of faults.

The QEMU register dump provided in the bug report illustrates the state of the system at the moment of the crash:

| Register | Value              | Description|
|----------|--------------------|------------|
| RIP      | 0xfffff8080002c47a | Virtual address of the instruction causing the fault
| RSP      | 0xfffff80800051030 | Current stack pointer in the kernel's virtual space
| CR0      | 0x80050011         | Paging enabled bit (WP, NE, ET, PE)
| CR3      | 0x000000007ce0d000 | Physical address of the corrupt/zeroed PML table
| EFER     | 0x0000000000000d01 | LMA (Long Mode Active) and NXE (No-Execute) bits set

Forensic examination of the memory at physical address 0x7CE0D000 using the QEMU monitor confirmed the hypothesis:

```
(qemu) xp /80x 0x7CE0D000
000000007ce0d000: 0x00000000 0x00000000 0x00000000 0x00000000
... (all zero entries)...
```

Because the PML table contained nothing but zeros, the CPU could not handle the resulting Page Fault (PF) because it
could not find the IDT or the code for the exception handler, leading instantly to a Double Fault (DF) and finally
a Triple Fault, which triggers a hardware reset. This bug was particularly difficult to diagnose because it was
a "delayed-action" failure; the system would appear to boot correctly until the Memory Manager reached a specific point
in its allocation sequence, making the crash seem non-deterministic.

### UEFI Memory Map Volatility and Buffer Overflows
Even as the CR3 reservation issue was being addressed, a second major challenge emerged related to the interaction
between the bootloader and the UEFI GetMemoryMap service. The UEFI specification requires the loader to allocate a buffer
large enough to hold the memory map, which consists of a series of descriptors representing every region of physical RAM.

The "hard buffer overflow" occurred because of a recursive side effect of the mapping process. In certain edge cases,
the act of mapping memory within the bootloader required the allocation of additional memory to store metadata or new
page tables. Under some UEFI implementations, these internal allocations would cause the UEFI firmware to update its own
internal memory map, potentially splitting existing entries or adding new ones to reflect the change in status.

If the loader had already calculated the required buffer size based on a previous call to ```GetMemoryMap()```, and
a subsequent allocation occurred before the final map was retrieved, the number of entries would increase beyond the
capacity of the allocated buffer. This led to a situation where the firmware would write more descriptors than the loader
had prepared for, resulting in a buffer overflow that corrupted adjacent memory structures in the loader's address space.

## Technical Innovations and Repository Evolution
Beyond the implementation of the Memory Manager, the ExectOS repository has seen a series of significant updates that
reflect a broader push toward code quality and architectural maturity.

### Enhancements to the Runtime Library (RTL)
The development of the Memory Manager was accompanied by significant refinements to the RTL. Specifically,
the standardization of the ```RTL::LinkedList::RemoveEntryList()``` routine signature and other related functions was
necessary to support the complex data structures required by the memory allocator.

In a kernel without a Memory Manager, linked lists are often static or use pre-allocated pools. With the introduction
of the non-paged pool, the kernel can now use dynamic linked lists to track free and used memory regions, manage object
handles, and maintain list-based queues for the scheduler. The standardization of these routines ensures that the Memory
Manager's internal bookkeeping is both reliable and performant.

### The Strategic Implementation of SHA-1 Hashing
An interesting addition to the RTL is the implementation of SHA-1 hashing support. While SHA-1 is no longer considered
secure for cryptographic signatures, its inclusion in the ExectOS likely serves a more practical, utility-driven purpose.
Hashing is a fundamental tool for data deduplication, file system integrity verification, and the generation of unique
identifiers for kernel objects.

## The Impact of the Memory Manager on the XT Architecture Roadmap
The implementation of the non-paged pool, while only a "basic" version of the Memory Manager, is the catalyst for the
next phase of ExectOS development. For several months, work on many critical kernel components had been stalled because
they lacked a mechanism to request memory at runtime.

### Supporting the Process and Thread Manager
The creation of a new process or thread requires the allocation of several structures, including the Process Environment
Block (PEB), the Thread Environment Block (TEB), kernel stacks, and the process's own page tables. The Memory Manager's
support for various paging levels (PML2 through PML5) is the foundation for this process creation logic. The kernel can
now allocate a physical frame for a process's top-level page table and begin building a private virtual address space.

### Enabling the Object Manager and Handle Database
In the XT architecture, almost all resources, like threads, events, and semaphores - are represented as objects. These
objects must be allocated from kernel memory and tracked within an object handle database. Before the Memory Manager was
integrated, the kernel could only support a fixed, hard-coded number of objects. With a functional non-paged pool now in
place, the way is clear to implement the Object Manager, which will dynamically allocate memory for new objects as
processes request them, allowing for a much more flexible and scalable system.

## Conclusion: A Milestone of Stability and Vision
The successful merger of the Memory Manager represents more than just the addition of a few thousand lines of code; it
is a validation of the XT architecture's design and the development team's ability to solve the most daunting problems
in low-level systems programming. By overcoming the CR3 corruption bug and the UEFI memory map issues, ExectOS has
demonstrated a level of robustness that is essential for any operating system aiming for general-purpose use.

As ExectOS moves forward, the presence of the Memory Manager will allow the focus to shift toward user-mode subsystems,
driver compatibility layers, and the native application interface. The foundation is now set for ExectOS to transition
from a kernel that simply boots to an operating system that can truly execute. The journey to this point was longer than
expected, but the lessons learned in the process have resulted in a kernel that is more resilient and better prepared
for the challenges of contemporary hardware.
