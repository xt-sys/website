+++
title = 'XTchain 3.0.2 Released: New Disk Image Tools'
author = 'Aiken Harris'
date = '2025-09-30T06:12:05+01:00'
+++
We are excited to announce the release of XTchain 3.0.2. While this update is relatively minor, it brings several
important improvements aimed at enhancing Windows compatibility and streamlining workflows across platforms.
<!--more-->

## Integrated Mtools for FAT Images
XTchain 3.0.2 now comes with Mtools built-in. This integration allows developers to format disk or partition images with
the FAT filesystem and copy data to and from those images directly - no additional setup required.

## New diskimg Utility
Alongside Mtools, we have introduced our own diskimg application. This tool simplifies the creation of disk images,
particularly on Windows systems where native support can vary depending on configuration. By bundling these tools
directly into XTchain, we ensure that ExectOS builds can be created consistently across Linux and Windows environments.

## LLVM Updated to 21.1.2
This release also includes an update to LLVM 21.1.2, bringing the latest compiler improvements and fixes into XTchain.

## Important Note for ExectOS Developers
Version 3.0.2 is required to successfully build ExectOS. Older versions of the toolchain will not be able to generate
disk images, which will cause the diskimg target to fail.
