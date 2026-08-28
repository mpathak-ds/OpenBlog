---
title: "Vectra Open Sourced (ARM)"
pubDate: 2026-08-28
description: "Vectra finally open sourced, ARM port, and more"
author: "Mayank Pathak (Lead Engineer)"
---

# Summary of changes

Welcome to today's blog - and first of all, sorry for missing to post a blog yesterday - trust me, I was really busy with life :(.

Aside from my totally very formal apology, some major things have happened meanwhile. First and foremost being Vectra being open-sourced! But, the ARM branch, and from the very beginning instead as it is much harder to port the existing x86_64 branch and there are some conflicts between x64 and ARM/RISC/etc branches that I want to make in the core system. Therefore, it's from a clean slate. A major addition is the system being almost entirely in C++, instead of C. It is also extremely cross-platform and modular, it already runs on both ARM64 target platforms: QEMU `virt`, and the **Orange Pi 3B**. Yep, it's running on real hardware! The kernel, hypervisor and coreOS is entirely separated from the bootloader and CPU-specific aspects. It is live and public on GitHub, right now [here](https://github.com/mpathak-ds/Vectra).

# Philosophy

The same philosophy, as discussed in our very [first blog](https://driftlessblog.netlify.app/blog/first-post/), applies to here as well. There is no UNIX/POSIX/NT/etc baggage, every subsystem and kernel component is thought out without compatibility in mind and only modern hardware in mind. A major example of this is the **Object Manager** - it is the heart of the system itself. Almost every subsystem depends on it, and every kernel-managed resource is an *object*. It is capability-based, and every subsystem must inherit from it, avoiding a scenario of 3 different bugs of reference counting into just 1 core bug in the manager. However, there is a paradox here, a chicken and egg one. If the memory manager also uses objects for regions and allocations, but the object manager needs the memory manager to allocate its objects.. you can see the problem. Let's see...

# The Solution

The easy fix, as implemented in [bootalloc.c](https://github.com/mpathak-ds/Vectra/blob/main/base/mm/bootalloc.cpp) - simply implement a bootstrap allocator with the dumbest design ever, and use that to bootstrap your early physical allocator, which you use for the object manager, so you can use objects in your later, primary memory manager component.

# Credits where due

I have to give my credits to my partner, `daniilfigasystems` (or just Danil) who has been extremely helpful ever since the beginning, and implemented the ARM `GICv2` driver for `virt` systems as well as proper logging support, provided the real hardware development board and helped a lot in the x64 branch as well. Hats off to him!

# Conclusion

We really hope you will enjoy the development progress and the system itself - you are free to leave any comments, suggestions, or criticism at our email: [driftlesssoftware@gmail.com](mailto:driftlesssoftware@gmail.com).

We check it everyday :D!