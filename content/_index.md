+++
title = 'About Exectos'
date = 2024-06-16T12:12:40+02:00
+++
## About
ExectOS aims to be a stable and flexible, general purpose operating system written from scratch. It is designed to be
modular, maintainable and compatible with existing software. It implements a brand new XT architecture and features own
native application interface. On the backend, it contains a powerful driver model between device drivers and the kernel,
that enables kernel level components to be upgraded without a need to recompile all drivers.

## XT Architecture
ExectOS is a preemptive, reentrant multitasking operating system that implements the XT architecture which derives from
NT&trade; architecture. It is modular, and consists of two main layers: kernel and user modes. Its' kernel mode has full
access to the hardware and system resources and runs code in a protected memory area. It consists of executive services,
which is itself made up on many modules that do specific tasks, a kernel and drivers. Unlike the NT&trade;, system does
not feature a separate Hardware Abstraction Layer (HAL) between the physical hardware and the rest of the OS. Instead, XT
architecture integrates a hardware specific code with the kernel. The user mode is made up of subsystems and it has been
designed to run applications written for many different types of operating systems. This allows us to implement any
environment subsystem to support applications that are strictly written to the corresponding standard (eg. DOS, or POSIX).

## Features
 * Completely free, Open Source and community-driven modern Operating System.
 * Native multiple architecture support. It runs on x86 and x86_64 and is portable to other architectures.
 * Hardware layer API allows for a single unified kernel. There is no need to maintain a separate kernel forks.
 * Uses a couple of ideas from other open source projects, but it’s our own ideas that really sets us apart and defines
   ExectOS. The majority of code is original, not forked.
 * Modular design for better compatibility with existing software and drivers, enabling binaries to remain compatible even
   when the core system is updated.
 * Own, XT drivers for commonly used devices as well as NT&trade; drivers compatibility layer for better hardware support.
 * Technical support directly from the architects and developers of the system.

## Why
 * We believe, there is no ideal Operating System on the market. During ExectOS development, we try to bring most useful
   features known from existing solutions, while keeping compatibility with NT&trade; architecture at desired level.
 * Some of our ideas differ greatly from other projects and it is much easier if we do not have to fight legacy code and
   ideas.
 * We need the freedom to break things when necessary.

## Goals
 * Keep the greatest advantages of the NT&trade; architecture, while implementing new features and technologies known from
   other Operating Systems.
 * Flexibility where it makes sense to provide flexibility.
 * Be able to deliver features that other non-commercial Operating Systems consider too ambitious.

## Requirements
ExectOS is in very early development stage, thus its requirements have been not specified yet. However according to its
design, it requires a modern EFI enabled hardware. It is not possible currently to boot ExectOS on a legacy BIOS.
