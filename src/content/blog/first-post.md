---
title: "What is this all about?"
pubDate: 2026-08-25
description: "What the hell is VectraVMM? Why does it exist? And.. what is this website?"
author: "Mayank Pathak (Lead Engineer)"
---

# Hello World!

Welcome to OpenBlog. This is a website intended to document progress for *VectraVMM* and related projects from **Driftless Software** (the team responsible for VectraVMM) directly. In this post I will be answering some of the primary questions you might have in mind, hence the title.

# Who am I?

I'm Mayank Pathak, the **Lead Engineer** of VectraVMM and a fellow developer in the OpenXP team. My job is to ensure development of projects like VectraVMM go smoothly and properly - and develop the core architecture of it.

# What is VectraVMM?

VectraVMM is a from-scratch and clean-slate **Type 1 Hypervisor Operating System** that aims to run both Windows and Linux guest subsystems with low latency. At first glance, this may seem like an impossible task - or so I've been told alot. However regardless, we started development of this back in February of 2026 and got a minimal viable prototype (*MVP*) ready by March. Screenshot attached below!

![VectraVMM MVP Screenshot](/images/image1ss.png)

```c
SSDT: KiSystemCallHandler reached with service id 4378
SSDT: Calling NT dispatcher
SSDT: NtGdiSetPixel called with parameters (1, 600, 400, 255)
WIN32K: NtGdiSetPixel called
SSDT: KiSystemCallHandler reached with service id 17
SSDT: Calling NT dispatcher
SSDT: KiSystemCallHandler reached with service id 4
SSDT: Calling Linux dispatcher
SSDT: LinWrite called with parameters (1, Dark!
, 7)
Dark!
```

As you can see, it runs both Linux and Win32 applications - although in a swappable SSDT design. This was slow (not noticeable, but in benchmarking) relative to what would be the theoretical speeds if hardware-accelerated by Intel VT-x Technology. So we settled on a hardware-based hypervisor design instead and started rewriting the system - now known as *VectraVMM*. It is also built with modern hardware in mind without any regard for legacy compatibility with UNIX or related operating systems at the core as that is already handled by guest subsystems - discussed in the next section.

# Is it just Linux but fancy?

No. We built the core of VectraVMM keeping in-mind that there is no need for POSIX or NT compatibility, these are already handled by `VMMON` (Guest Subsystem Manager), hence the core is free of any compatibility hurdles. We took this opportunity to strip away legacy UNIX-like concepts from the era of 64KB address spaces such as "everything is a file". The kernel is purely designed based on capability systems rather than legacy overhead.

# Open source?

Partially. VectraVMM's core is *not* open source **yet**, however the guest subsystems are fully open source and transparent on GitHub ([HyperNT](https://github.com/mpathak-ds/HyperNT)). Regardless, both of their progress and logs are posted on this blog. We are also more than happy to answer questions of the internals if you ask us via email or social media!

# AI?

No. Matter of fact, we have a strict anti-AI [policy](https://github.com/mpathak-ds/HyperNT/blob/main/AGENTS.md) on the open-source subsystems. We believe that AI-generated code is **NOT** welcome to safety-critical systems and should always be human-written and evaluated.

# Conclusion

We really hope you will enjoy the development progress and the system itself - you are free to leave any comments, suggestions, or criticism at our email: [driftlesssoftware@gmail.com](mailto:driftlesssoftware@gmail.com).

And yes, we are an officially-registered software startup aswell!