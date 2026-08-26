---
title: "Vectra Driver Model Framework"
pubDate: 2026-08-26
description: "The driver model framework for Vectra, refactoring and more"
author: "Mayank Pathak (Lead Engineer)"
---

# Today's additions

Welcome to the post, here we will summarize and talk about what was done today in the development of VectraVMM. Today we have refactored the `VMMON` component (Guest Subsystem Manager) to be cleaner and neater in code, making it more maintainable. Along with this a major part of today's work was to implement the **Vectra Driver Model Framework** otherwise known as *VDMF*. This allows to implement drivers easily into the core with a standardized framework and overall make the system more modular. In the next sections, these additions and changes will be explained in more depth.

![VectraVMM Commits Screenshot](/images/image2ss.png)

# VMMON Refactoring

`VMMON` was not very modular in terms of code earlier, and it was clumped in giant two to three code files. Ofcourse that's not very neat or clean in any way, so it was refactored into multiple, more specific and modular code files with better documentation and commentation.

![VectraVMM VMMON Screenshot](/images/image3ss.png)

All of the files now have more specific and better commenting for each.

# VDMF Implementation

We have started to implement VDMF into the core for modularity and standardization - the aim is to give all drivers a single framework to communicate with the kernel and eachother. As of right now, the implementation relies on a **driver registry** where each driver has their own descriptor (`kern_drv_t`) with the physical device(s) they are written for (`phys_device_t`). Each physical device contains their kernel handle, device ID, vendor ID and an **optional** human-readable device name. If `dev_name` is left blank, it automatically is set to `Unknown Device`.

Each driver has a pointer or array to the physical device(s) that they can handle or are written for, a field for the name of the driver (again, if left blank, translates to `Anonymous`) and a driver ID that is *assigned by the framework* and not the driver itself. They must support three core functions: `init`, `probe` and `remove`. The last two take a pointer to their physical device.

The job of `init` is to initialize any **driver-specific global initialization** and is called while registering the driver (elaborated later on). This might include stuff like setting up data descriptors or a log setup.

The responsibility of `probe` is to handle **per-device initialization** and is automatically called by the framework for each physical device that the driver supports. This *should* include device setup and initialization.

Similarly but conversely, `remove` is automatically called by the framework for each physical device of the driver that needs to shutdown.

The framework directly exposes two core functions for every driver during bringup:

```c
OWSTATUS vdmf_register_driver(kern_drv_t *driver);
OWSTATUS vdmf_unregister_driver(kern_drv_t *driver);
```

As implied, `vdmf_register_driver` registers the driver into the registry and calls `init` function of that driver. Note this does not deal with device initialization yet and `probe` is automatically called upon detection of a device that the driver supports via PCI or PCIe enumeration and other methods. Converse for `vdmf_unregister_driver`.

Here is a neat dummy driver example of using the framework!

```c
#include <drivers/base.h>

OWSTATUS dummy_init()
{
    klog_info("DUMDRV: In init function!");
    return OW_STATUS_SUCCESS;
}

OWSTATUS dummy_probe(phys_device_t *dev)
{
    klog_info("DUMDRV: Probing device ID=%d (%s)", dev->dev_id, (!dev->dev_name) ? "Unknown Device" : dev->dev_name);
    return OW_STATUS_SUCCESS;
}

OWSTATUS dummy_remove(phys_device_t *dev)
{
    klog_info("DUMDRV: Removing device ID=%d (%s)", dev->dev_id, (!dev->dev_name) ? "Unknown Device" : dev->dev_name);
    return OW_STATUS_SUCCESS;
}

void dummy_test()
{
    phys_device_t dumdev = {
        .kern_id = 1,
        .vendor_id = 0x45F,
        .dev_id = 0x33,
        .dev_name = "Dummy Device"
    };

    kern_drv_t dumdrv = {
        .device = &dumdev,
        .drv_name = "Dummy Driver",
        .drv_id = NULL, //set by framework
        
        .init = dummy_init,
        .probe = dummy_probe,
        .remove = dummy_remove
    };

    vdmf_register_driver(&dumdrv);
}
```

More is to be added and demonstrated in later blog writeups, however I will leave it up to here for today.

# Conclusion

We really hope you will enjoy the development progress and the system itself - you are free to leave any comments, suggestions, or criticism at our email: [driftlesssoftware@gmail.com](mailto:driftlesssoftware@gmail.com).

We check it everyday :D!