+++
title = 'Migration of the Project to C++ with Maintained C Compatibility'
author = 'Aiken Harris'
date = '2025-09-28T17:12:45+01:00'
+++
The project’s kernel, bootloader, and all associated modules have been migrated to C++. This transition was a complex
and time-consuming effort that we deliberately prioritized over adding new features. Because the codebase was still
relatively compact, undertaking this migration at this stage was significantly easier than it would have been later.

In total, the migration involved modifications across **297 files**, with *16,671* lines of code added and *13,312*
lines removed. The process culminated in the merge of **67 commits**. Despite these internal changes, the public
interface exposed through XTDK remains fully compatible with C. As a result, developers can write drivers in either C
or C++.

## Benefits of the Migration
Although certain C++ features such as global constructors, destructors, runtime type information and exceptions are not
suitable for use in the kernel, a large subset of the language can be used  safely and effectively. Adopting C++
internally allows us to benefit from:

- **Stronger static type checking** – templates, references and stricter type rules help eliminate entire classes of
  low-level bugs before they reach runtime.

- **Lightweight classes and structs** – we can define clear interfaces and encapsulate data without relying on heavy
  runtime mechanisms. Member functions and inline methods provide better organisation than scattered C functions.

- **Namespaces for isolation** – by grouping related functionality into namespaces we avoid symbol collisions, keep the
  global namespace clean and make it easier to navigate the codebase.

- **Inline and template-based utilities** – compile-time polymorphism and generic programming allow common patterns to
  be written once and reused without runtime overhead.

- **Clearer modular boundaries** – internal implementation details can be hidden behind class interfaces, improving
  encapsulation and making each subsystem easier to reason about and maintain.

This approach preserves the deterministic behaviour and performance required by kernel code while still providing
a modern, well-structured environment for development. It enables a codebase that is easier to extend, review and
maintain, without introducing mechanisms that are unsafe or impractical at the system level.

## Preserving C Compatibility
Full C compatibility has been retained through the use of **C wrappers** around the underlying C++  components. This
design choice means that the migration does not affect how external code interacts  with the system.

Although the project is still in its initial phase and no drivers have been released yet, the  architecture is prepared
so that drivers can be written in **both C and C++** without restriction.

The SDK remains accessible to developers who prefer C, while at the same time opening the door for developers writing in
C++ to integrate with the system using native language features. This  approach ensures a stable and predictable API
over time and provides flexibility for future contributors regardless of their chosen language.

## C Wrappers and Linker Optimisations
The C wrappers are implemented as lightweight functions declared with `extern "C"` forwarding calls to the C++
implementation. When building with **clang-cl**, linker flags help ensure these wrappers have negligible impact on
performance and binary size:

- **`/OPT:REF`** (Remove Unreferenced Functions and Data) directs the linker to eliminate functions and data sections
  that are not referenced anywhere in the binary. This reduces the size of the resulting kernel and modules by
  discarding unused wrappers or internal routines.

- **`/OPT:ICF`** (Identical COMDAT Folding) merges byte-for-byte identical functions or sections into a single instance.
  In practice, thin C wrappers calling the same C++ routine can be folded together, removing redundancy and avoiding
  unnecessary call layers.

With these optimisations, the system delivers the advantages of a C++ implementation while maintaining a stable,
efficient C interface for developers.
