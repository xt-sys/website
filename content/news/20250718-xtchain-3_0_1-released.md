+++
title = 'XTchain 3.0.1 Released: Bug Fixes & Updates'
author = 'Aiken Harris'
date = '2025-07-18T10:54:37+02:00'
+++
Just days after the milestone release of XTchain 3.0.0, we have launched version 3.0.1, delivering targeted improvements
and essential bug fixes to correct initial regressions and improve overall functionality across platforms.

XTchain 3.0.1 may appear as a minor version bump, but its release demonstrates the project's commitment to rapid iteration
and user-focused refinement. The update includes the latest versions of LLVM, Ninja and Wine tools (such as widl, wmc and
wrc) - three essential tools in the modern compilation ecosystem. These updates were integrated specifically in response
to the recent upstream releases, ensuring XTchain remains compatible with the most up-to-date and performant build
infrastructure.

While 3.0.0 marked a significant leap forward for the project - introducing a reworked toolchain architecture and broader
platform support - version 3.0.1 builds on that foundation by focusing on polish, reliability, and cross-platform parity.

#### Key Highlights in XTchain 3.0.1
 * **Bug Fixes Across the Board**: We addressed several issues uncovered after the 3.0.0 release, including a lingering
   problem where XTchain attempted to display the version of the non-existent windres tool - an issue that stemmed from an
   incomplete cleanup of deprecated tool references.
 * **Improved Windows Environment Support**: One of the top user-requested features has landed: XTchain now works seamlessly
   within GIT Bash on Windows. This enhancement makes it easier for developers on Windows to interact with the toolchain
   using familiar Unix-like workflows. Additionally, a new script has been included to help initialize the build environment
   automatically.
 * **Enhanced XBUILD Behavior**: A particularly frustrating issue with the xbuild command - where it silently ignored
   user supplied parameters has been resolved. The tool now correctly passes all build arguments, ensuring predictable
   behavior for more complex project setups.
 * **Unified Cross-Platform Experience**: A key development goal in 3.0.1 was to harmonize behavior across Linux and Windows
   environments. From consistent output formatting to standardized toolchain handling, users can now expect a much more
   uniform experience regardless of their platform of choice.

Users are encouraged to update as soon as possible to benefit from the improved tooling, better compatibility, and refined
user experience.
