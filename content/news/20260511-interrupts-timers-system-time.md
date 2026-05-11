+++
title = 'Interrupts, Timers and System Time Infrastructure'
author = 'Aiken Harris'
date = '2026-05-11T21:33:37+01:00'
+++
Since commit 64b5de98c8 from March 27, 2026, the ExectOS kernel has received a substantial update focused on interrupt
handling, hardware timer infrastructure, system time management, RTC support, and early SMP-related functionality.
The work completed during this development cycle establishes the first fully integrated timing subsystem within the
kernel and expands the low-level x86 hardware support. This update lays the essential groundwork for true multi-tasking
and multi-core execution in ExectOS.
<!--more-->

## Interrupt Handling Infrastructure
### Unified Interrupt Handlers
One of the largest architectural changes introduced during this development cycle is the redesign of the interrupt
handling path for both AMD64 and i686. The previous interrupt handling implementation was replaced with a unified
dispatcher-based model. Low-level assembler stubs now save processor context in a standardized format before
transferring control to common architecture-independent dispatching routines. The assembler entry code was heavily
reworked for both supported architectures. The changes include:
 * Unified interrupt stack frame layout
 * Consistent register preservation rules
 * Correct interrupt frame alignment
 * Improved exception and IRQ separation
 * Common interrupt dispatch path
 * Simplified vector handling logic
 * Removal of duplicated boot and trap entry code

The redesign reduces duplicated logic between AMD64 and i686 while also simplifying future SMP and scheduler integration.

### Interrupt Vector Registration
A new interrupt registration subsystem was added to allow runtime installation of handlers for interrupt vectors. The
kernel now maintains a centralized interrupt dispatch table which associates vectors with architecture-independent handler
routines. This replaces static or hardcoded interrupt paths and allows dynamic registration of:
 * Hardware IRQ handlers
 * APIC interrupt vectors
 * Timer interrupts
 * IPI handlers

The new mechanism simplifies hardware abstraction and makes it possible to register different interrupt providers depending
on detected hardware capabilities.

### Interrupt Dispatching
A dedicated dispatch layer was added in the kernel execution subsystem. This layer is responsible for:
 * Interrupt vector validation
 * Context handoff between architecture and kernel layers
 * Dispatching registered handlers
 * Managing interrupt acknowledgment flow
 * Supporting future deferred interrupt processing

This work also prepares the kernel for future scheduler preemption and SMP-aware interrupt routing.

## I/O APIC Support
### I/O APIC Integration
The hardware layer received major updates related to interrupt controller support. The kernel now supports I/O APIC-based
interrupt routing. ACPI MADT parsing was extended to enumerate:
 * Local APIC controllers
 * IO APIC controllers
 * IRQ overrides
 * APIC interrupt routing information
 * Global System Interrupts (GSI)

Unlike the legacy PIC architecture, modern x86 systems frequently expose more than one I/O APIC controller. ExectOS already
supports enumeration and initialization of multiple I/O APIC units and dynamically selects the correct controller depending
on the GSI range associated with a given interrupt source.

The I/O APIC subsystem internally operates on Global System Interrupts rather than legacy ISA IRQ numbers. A GSI represents
a globally unique interrupt line within the system interrupt topology. Instead of routing interrupts through fixed ISA IRQs
(0-15), modern firmware exposes interrupt routing information through ACPI tables which map devices to GSIs. This design allows:
 * More than 16 interrupt lines
 * Flexible interrupt routing
 * Multiple interrupt controllers
 * Interrupt source overrides
 * APIC-based SMP interrupt delivery

The kernel translates legacy IRQs into GSIs using ACPI MADT override entries before configuring I/O APIC redirection tables.
The I/O APIC initialization code dynamically configures redirection entries and routes hardware interrupts through APIC
infrastructure.

### Interrupt Routing Improvements
Several interrupt routing issues were fixed during the I/O APIC implementation work:
 * Correct handling of IRQ source overrides
 * Proper GSI translation
 * Correct I/O APIC controller selection
 * Proper vector remapping
 * APIC interrupt masking fixes
 * Interrupt acknowledgment corrections
 * Improved initialization ordering
 * Consistent handling across AMD64 and i686 architectures

These changes were required to ensure reliable interrupt delivery.

## Clock and Timer Infrastructure
### Clock vs Timer
ExectOS now distinguishes between two independent concepts:
* Clock device
* Timer device

A clock device generates periodic interrupts at a fixed frequency. These interrupts are used to maintain kernel timekeeping,
scheduler ticks, and periodic accounting. A timer device is responsible for precise time measurement. Unlike a clock source,
a timer does not necessarily generate periodic interrupts. Instead, it provides accurate time interval measurements used for:
* High resolution timekeeping
* Performance counters
* Timeout calculations
* Precise delay measurements
* Thread accounting

This distinction allows ExectOS to combine different hardware sources depending on hardware capabilities. For example: ARAT
may be used as the periodic clock source and Invariant TSC may be used as the high precision timer source.

### Supported Clock Devices
The kernel currently supports the following clock sources:

|Clock Source                     | Description                                                         |
|---------------------------------|---------------------------------------------------------------------|
|ARAT (Always Running APIC Timer) | Local APIC timer operating independently from CPU frequency changes |
|HPET                             | High Precision Event Timer operating in periodic mode               |
|PIT                              | Legacy Programmable Interval Timer fallback                         |


### ARAT Support
The preferred clock source in ExectOS is the Local APIC timer operating in ARAT mode. Always Running APIC Timer guarantees
that the Local APIC timer continues operating at a constant rate regardless of:
 * CPU frequency scaling
 * Power management transitions
 * Turbo boost state changes
 * Deep sleep states

This makes ARAT reliable for periodic scheduler and system clock interrupts. The kernel intentionally avoids using the
traditional Local APIC timer when ARAT capability is not available. Without ARAT support, the APIC timer frequency may
drift when processor frequency changes occur, causing inaccurate scheduler timing and unstable system time accounting.
If ARAT is unavailable, the kernel automatically falls back to another supported clock device.

### HPET Clock Mode
HPET can also operate as the kernel periodic clock source. HPET is a modern hardware timer subsystem introduced to replace
older legacy timing devices such as the PIT and RTC periodic interrupts. The HPET implementation includes:
 * ACPI HPET table parsing
 * Frequency detection
 * Comparator configuration
 * Periodic interrupt generation
 * Interrupt routing integration

When used as a clock source, HPET periodically generates interrupts at a configured frequency used by the kernel scheduler
and system clock subsystem. HPET provides significantly better precision than the legacy PIT while remaining independent
from CPU frequency scaling.

### PIT Fallback
The PIT implementation remains available as a compatibility fallback for systems lacking HPET or ARAT support. The
Programmable Interval Timer is one of the oldest timing devices present on x86 systems. It operates using a fixed hardware
frequency derived from a 1.193182 MHz oscillator and provides periodic interrupts through legacy IRQ routing. Although
PIT is considerably less precise and introduces higher interrupt latency, it guarantees compatibility with older x86
hardware and virtualized environments. Due to limited precision and relatively high interrupt overhead, PIT is used only
as a last-resort fallback device when modern timing hardware is unavailable.

### Supported Timer Devices
The kernel currently supports the following timer devices:

|Timer Source  | Description                      |
|--------------|----------------------------------|
|Invariant TSC | Constant-rate Time Stamp Counter |
|HPET          | High Precision Event Timer       |
|ACPI PM Timer | ACPI Power Management Timer      |
|PIT           | Legacy PIT fallback              |

### Invariant TSC Support
Invariant TSC is the preferred high precision timer source in ExectOS. Invariant TSC guarantees that the processor
Time Stamp Counter increments at a constant rate regardless of:
 * CPU frequency scaling
 * C-state transitions
 * Turbo frequencies
 * Power management state changes

This allows the kernel to use the TSC as a stable high resolution timer. The kernel intentionally refuses to use
a non-invariant TSC. Traditional TSC implementations may drift or change frequency dynamically depending on processor 
tate. Using a non-invariant TSC would produce unreliable timing results and incorrect system time calculations. If
the processor does not provide Invariant TSC support, the kernel falls back to another supported timer source.

### HPET Timer Mode
HPET may also operate as a high precision timer source. In timer mode, HPET is used as a continuously running hardware
counter rather than a periodic interrupt generator. The counter is driven by a fixed-frequency oscillator and operates
independently from processor frequency scaling, power management transitions, and CPU sleep states. This makes HPET
a stable and monotonic timing source suitable for high resolution elapsed time measurements and timer calibration.
The HPET implementation supports:
 * Main counter access
 * Frequency conversion
 * Nanosecond-scale timing calculations
 * High precision interval measurements

Unlike TSC, HPET access requires memory-mapped I/O operations, resulting in higher read latency compared to the RDTSC
instruction. Despite the additional overhead, HPET remains one of the most reliable fallback timing sources on x86
systems because its frequency remains constant regardless of processor state changes.

### ACPI PM Timer
The ACPI Power Management Timer is implemented as an additional fallback timing source for systems lacking reliable
Invariant TSC or HPET support. The timer operates as a free-running monotonic counter clocked at approximately
3.579545 MHz and is exposed through ACPI firmware interfaces. Unlike traditional TSC implementations, the ACPI PM timer
is completely independent from processor frequency scaling and power management behavior.
The implementation includes
 * ACPI FADT integration
 * Counter rollover handling
 * Frequency normalization
 * Stable elapsed time calculations

Although the ACPI PM timer provides significantly lower precision and higher access latency than HPET or Invariant TSC,
it remains highly reliable due to its fixed-frequency operation. It is also useful during early kernel initialization
and on virtualized systems where TSC behavior may be inconsistent or unstable.

### PIT Timer Mode
PIT timing support remains available as a final compatibility fallback. Although low precision, it allows the kernel to
maintain functional timing support even on minimal hardware configurations.

## Kernel Boot Parameters
### CLOCK Parameter
The kernel now supports the **clock=** boot parameter used to select the preferred periodic clock source responsible
for generating regular system clock interrupts. Supported clock devices currently include: arat, hpet, pit. The selected
clock source is responsible for generating periodic interrupts. The kernel validates hardware capabilities before
activating the requested clock device. For example, clock=arat succeeds only if:
 * Local APIC support is available
 * The processor exposes ARAT capability

If the selected clock device does not satisfy kernel requirements, initialization automatically falls back to the next
supported clock source available on the system. This allows deterministic clock selection while preserving compatibility
across different hardware configurations.

### TIMER Parameter
The **timer=** boot parameter selects the preferred high precision timer source used for elapsed time measurements and
runtime timing calculations. Supported timer devices currently include: tsc, hpet, acpi, pit. The kernel again validates
hardware capabilities before enabling the selected timer source. For example, timer=tsc succeeds only if the processor
provides Invariant TSC support. If the selected timer source does not satisfy kernel timing requirements, the kernel
automatically falls back to another supported timer device.

## RTC and Timekeeping
### RTC Support
RTC support was implemented as part of the new timekeeping subsystem. The Real-Time Clock is used primarily during system
startup to obtain the initial wall-clock time. The implementation includes CMOS RTC access, BCD to binary conversion,
update-in-progress synchronization, date normalization, and integration with kernel system time initialization routines.
The RTC is not used as the primary runtime timing source. Instead, runtime timekeeping is maintained using the active
clock and timer infrastructure. The RTC may later be queried periodically to compensate for timer drift on inaccurate
hardware configurations.

### XT Epoch and Unix Epoch
Unix Epoch represents time as the number of seconds elapsed since January 1, 1970, 00:00:00 UTC. This representation is
commonly used by Unix and BSD operating systems as well as POSIX-compatible interfaces. ExectOS internally uses XT Epoch
as its native kernel time representation. XT Epoch is based on January 1, 1601, 00:00:00 UTC and stores time using
100-nanosecond intervals instead of seconds. The runtime library and kernel now provide complete conversion support
between multiple time representations, including:
 * RTC calendar time fields
 * Unix Epoch
 * XT Epoch

The conversion layer performs normalization and translation between second-based Unix timestamps and the native
100-nanosecond XT Epoch format used internally by the kernel. This functionality allows ExectOS to maintain a high
precision internal time representation while preserving compatibility with Unix and BSD-compatible interfaces and future
POSIX-style APIs.

## System Time and Scheduler Integration
One of the most significant milestones introduced during this development cycle is the first fully integrated system time
infrastructure within the ExectOS kernel. The kernel now processes periodic clock interrupts generated by the selected
clock device and uses them to maintain runtime timing state. Each clock tick advances the internal system clock and
provides the timing foundation required for scheduler operation and runtime accounting.

This work also introduced the initial thread runtime accounting implementation. The kernel can now track execution time
consumed by threads and maintain basic runtime statistics required for future scheduler development. Although the
scheduler itself is still under development, the timing infrastructure required for preemptive multitasking, quantum
accounting and timeout handling is now in place.

## Inter-Processor Interrupts (IPI)
Initial Inter-Processor Interrupt support was implemented as part of the Local APIC infrastructure redesign. The kernel
now contains the low-level mechanisms required to deliver interrupts between processors using the APIC Interrupt Command
Register (ICR). The implementation introduces vector-based IPI dispatching infrastructure together with helper routines
responsible for APIC IPI delivery and interrupt routing. This work is primarily intended as groundwork for future SMP
support and multi-core scheduler integration.

Although ExectOS does not yet fully initialize secondary processors, the current implementation establishes the required
architecture for future processor startup sequencing, cross-core synchronization, deferred work scheduling, and
inter-processor signaling.

The APIC subsystem was also refactored to provide cleaner interrupt routing semantics and more consistent behavior
between AMD64 and i686 implementations. Several issues related to interrupt acknowledgment, APIC vector initialization,
and interrupt masking behavior were corrected during this process.

## Additional Low-Level Improvements
Besides the interrupt and timing infrastructure work, this development cycle also introduced substantial cleanup and
refactoring across multiple kernel subsystems.

The ACPI subsystem received multiple improvements related to MADT, FADT, and HPET table parsing together with expanded
hardware discovery support required for APIC and timer initialization. Processor support code was reorganized and
extended to improve feature detection, APIC capability handling, and timing source validation.

The kernel dispatching infrastructure was also expanded in preparation for future scheduler integration. Several internal
runtime structures were redesigned to better support interrupt-driven execution flow, runtime accounting, and future
deferred execution mechanisms.

## What's Next
The current state of the ExectOS kernel now includes fully functional interrupt dispatching infrastructure, dynamic
interrupt handler registration, IO APIC interrupt routing, periodic system clock operation, runtime system time updates,
RTC-based wall clock initialization, and high precision timer support using multiple hardware timing sources.

The next stage of development will focus primarily on SMP implementation, which is already reflected by the creation of
a dedicated development branch for multi-processor support. The interrupt, APIC, timer, and IPI infrastructure introduced
during this development cycle was designed specifically to provide the low-level foundation required for future multi-core
execution.

Following SMP bring-up, development efforts will shift toward thread management, scheduler infrastructure, and context
switching support. With the core timing and interrupt subsystems now operational, the kernel is approaching the stage
where fully interrupt-driven task scheduling and multi-threaded execution can be implemented on top of the existing
architecture.
