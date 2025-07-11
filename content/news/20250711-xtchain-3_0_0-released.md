+++
title = 'XTchain 3.0.0 Released: Now Available for Windows'
author = 'Aiken Harris'
date = '2025-07-11T09:04:17+02:00'
+++
We are proud to announce the release of XTchain 3.0.0, marking a major milestone in the evolution of the XTOS
build toolchain. This new version introduces a streamlined experience, improved platform support, and updated core
components - further solidifying XTchain's role as a reliable foundation for building the ExectOS operating system.

The new release is available now. Download XTchain 3.0.0 from the [release page](https://github.com/xt-sys/xtchain/releases/tag/3.0.0).
We invite developers and power users to explore the updated XTchain, try out the new Windows support or Lite editions, and
contribute to the growing ExectOS ecosystem.

#### What's New in XTchain 3.0.0
This release brings several key improvements and changes:
 * **Removal of mingw32:** XTchain no longer includes the mingw32 suite, significantly reducing complexity and size. Given
   that ExectOS uses PE executables but is not Windows, the inclusion of Windows-specific headers and libraries was deemed
   unnecessary.
 * **Toolchain simplification:** Redundant elements and legacy branding have been removed, making it easier for developers
   to integrate system-native tools if desired (not recommended for production use).
 * **Updated core tools:**
   * LLVM upgraded to 21.0.7
   * Wine tools upgraded to 10.11
   * CMake upgraded to 4.0.3
   * Ninja upgraded to 1.13.0
 * **Windows support:** For the first time, XTchain is officially available as a complete binary package for Windows,
   allowing users to build ExectOS natively on the platform.

#### Introducing the Lite Editions
XTchain 3.0.0 also debuts **Lite versions** of the toolchain. These minimalist editions include only the essential build
environment. The Lite editions are intended for advanced users who already have - or prefer to install - LLVM, CMake, and
Ninja from official sources. These variants offer flexibility without compromising the core functionality required to build
ExectOS.
