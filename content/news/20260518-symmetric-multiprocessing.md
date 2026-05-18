+++
title = 'Implementing Symmetric Multiprocessing (SMP) in ExectOS'
author = 'Aiken Harris'
date = '2026-05-18T08:03:43+01:00'
+++
Over the past week, across exactly 33 commits, the ExectOS kernel has reached a major architectural milestone: full
support for Symmetric Multiprocessing (SMP). The implementation encompasses the complete Application Processor (AP)
bootstrap process, including the real-mode trampoline code, the standard INIT-SIPI-SIPI sequence, and the allocation
and initialization of required per-CPU structures.
<!--more-->

To facilitate kernel debugging and testing, we also introduced the MAXCPUS boot parameter. This feature allows us to
dynamically restrict the number of active logical processors during boot, down to a single core (the Bootstrap Processor,
or BSP). Being able to easily fall back to a non-SMP environment has proven invaluable for isolating race conditions and
verifying core kernel logic.

![ExectOS SMP](/images/exectos/exectos_smp.png)

While the fundamental SMP implementation was straightforward, bringing up secondary cores exposed a few subtle edge cases
regarding hardware initialization order and concurrency.

### The x2APIC State Mismatch
The most interesting issue encountered during the AP bring-up involved a General Protection Fault early in the AP
initialization phase, specifically related to x2APIC handling.

In ExectOS, both the BSP and the APs undergo a similar initialization routine, which includes setting the CPU runlevel.
During the BSP's early boot, the local APIC is not yet fully initialized. When the BSP attempts to change the runlevel,
it writes to the APIC via MMIO. Because the APIC is uninitialized, it simply ignores these writes. Later in the BSP's
boot process, the APIC is properly initialized. If the hardware supports x2APIC, the kernel enables it by setting bit 10
in MSR 0x1B and sets a global kernel flag indicating that, from now on, all APIC communication should be done via MSRs
rather than MMIO.

The bug manifested when the AP woke up. As the AP began its initialization, it attempted to set its runlevel. The kernel,
checking the global state, saw that x2APIC was enabled and attempted to use the wrmsr instruction to write to the x2APIC
Task Priority Register (TPR) at address 0x808. However, the AP had not yet enabled x2APIC in its own local MSR.
Attempting to write to an x2APIC MSR on a CPU that has not explicitly enabled it results in a GPF.

Interestingly, this issue only surfaced on the i686 architecture. On AMD64, changing the runlevel does not interact
directly with the APIC. Instead, Long Mode virtualizes the TPR via the CR8 register. Because AMD64 uses CR8 for runlevel
management, the operation bypasses the APIC entirely, completely masking the uninitialized hardware state.

We tracked this down using QEMU debug logs, which clearly pointed to the root cause:
```
check_exception old: 0xffffffff new 0xd
cpl=0 IP=0008:805cfa7f
EAX=00000808 ECX=00000808
```
 * **Exception 0x0D**: General Protection Fault.
 * **CPL=0**: Ring 0 (Kernel mode), ruling out privilege issues.
 * **ECX=0x808**: The IA32_X2APIC_TPR register. The presence of this value in ECX definitively confirmed that a wrmsr
   instruction triggered the fault.
Note: The behavior where the uninitialized xAPIC silently ignores MMIO writes may be specific to certain hardware or
emulation environments like QEMU. On different silicon, this might have triggered a bus error much earlier!

### Serializing Debug Output
The second challenge involved our debug logging mechanism. With multiple CPUs running concurrently, debug messages sent
to the serial port began to interleave, resulting in unreadable, garbled text.

To resolve this, we introduced a spinlock and a runlevel elevation inside the print function to serialize output and
protect against interrupts. However, this serialization introduced an unexpected initialization dependency during early
AP boot.

Before the APIC was initialized on the AP, the kernel was calling an early CPU initialization function. This function
was a stub, utilizing an UNIMPLEMENTED macro. The macro, by design, invoked the debug logger to report the missing
implementation. The logger then attempted to raise the runlevel, which as detailed above, attempted to write to MSR
0x808, instantly triggering the General Protection Fault.

### Proper CPU Feature Identification
We had two options to break this dependency loop: remove the UNIMPLEMENTED macro and ignore the missing logic, or
properly implement the CPU identification routine.

We opted for the architecturally sound approach. We implemented a comprehensive CPU identification and feature-detection
routine. During early initialization, the kernel now properly queries CPUID and populates the Processor Control Block
(PRCB) with all supported processor features and instruction sets.

By implementing this feature in full, we naturally removed the UNIMPLEMENTED macro, bypassing the early debug print, and
permanently resolving the initialization order conflict.
