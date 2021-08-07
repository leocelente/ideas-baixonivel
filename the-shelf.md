
# Table of Contents

1.  [Objective](#orgfb7edd5)
2.  [Categories](#org7adeb30)
    1.  [Driver](#orgdb88727)
        1.  [Summary](#org862a03d)
        2.  [Considerations](#org783e323)
        3.  [Technical Details](#org6b720e9)
    2.  [Interface](#orgb20bc24)
        1.  [Summary](#org8d11a2b)
        2.  [Considerations](#orgfb04300)
        3.  [Technical Details](#org3027426)
    3.  [Storage](#org523fabe)
        1.  [Summary](#org2399f0d)



<a id="orgfb7edd5"></a>

# Objective

Have a set of modular - and so maybe less efficient - software modules for use with
hardware. Those are divided in categories that specify an expected interface and
behavior


<a id="org7adeb30"></a>

# Categories


<a id="orgdb88727"></a>

## Driver


<a id="org862a03d"></a>

### Summary

This is the workhorse. Most of the code written is an low-level C code that
communicates with an specific hardware module, be it a sensor or actuators.
Being, probably, the biggest, this category is sub-divided in subcategories.
These are designed as <span class="underline">applications</span>. Not to be a fixed set, but to grow as
more applications are encountered. Here are some starting examples:

-   IMU
-   Motor
-   Temperature S.
-   Encoder
-   Power S.


<a id="org783e323"></a>

### Considerations

As the document title suggests, these are not tailor-made drivers, they aren't
supposed to fit every requirement of every project. Instead of the focus on
performance, the main goal is ease-of-use. These are meant to be general,
and as straightforward to use as possible. So each <span class="underline">application</span> should define
a set of minimal expectations, this means that features may be discarded as too
specific.

**This is to be expected**, there will be **no** mitigation. If your application is
 not served by the minimal driver it **will not** be part of The Shelf. It is a
 on off driver, that can (and probably will) be based on the shelf driver. But
 there should be no effort to keep it compatible, the author of said driver is
 completely free to do what it takes to fill the project requirements.


<a id="org6b720e9"></a>

### Technical Details

Again, these are general drivers, so there are some limitations on what will be
expected of them.

1.  Blocking

    If your application needs the use of interrupts or DMA, it will need to be
    thought out with the whole system in mind, they wont be general. This does
    not mean that the driver wont help, it is **strongly** advised that, data
    transactions an transformations be separated, so that one can more easily
    insert the core driver functionality in an asynchronous context.

2.  Platform Independent

    By this I mean that the code will not depend on platform specific code
    such as the STM32 HAL or Arduino Framework, there should be a file, not
    so aptly named BSP, that should wrap platform specific code in a common
    abstraction.
    
    -   Written in C

3.  Written in C/C++

    The driver must be written in C, however, it is encouraged that the
    application be defined as an C++ Class, so that higher level code can
    make use of OOP advantages.


<a id="orgb20bc24"></a>

## Interface


<a id="org8d11a2b"></a>

### Summary

This adapts the various methods of sending bytes between **micro-controllers**.
This could be the trickiest one to get right. In *in theory* this has to
abstract away details of protocols such as CAN, LoRa, Ethernet and Bluetooth.
Its intention is to serve as sinks of data for common applications.


<a id="orgfb04300"></a>

### Considerations

This will not be as generic as the drivers, it will require that functions
be called at, for example, certain interrupts. I wont lie, the main focus is
on radios and CAN, the rest is mostly an afterthought. The key concepts that
should be defined are of a <span class="underline">packet</span>, and of <span class="underline">handlers</span>.

1.  Packet

    This is self-explanatory, this is a general bag of bytes. However, things get
    interesting once we require that data may be **broken** in packets, and so, these
    packets have to be **reassembled** in order to reconstruct the data, and handle
    cases of missing packets. But what happens if you simply add all this protocol
    abstraction on top of something like LoRa or even CAN, you start to accumulate
    losses. So it is the challenge to adapt these common requirements to each
    base protocol.

2.  Handlers

    These are simply the hooks for arriving and outgoing packets. Function pointers
    simply.


<a id="org3027426"></a>

### Technical Details

There will be two layers to this. The low-level drivers, that should follow
the same philosophy as Shelf Drivers, with the obvious exception that they
will not be Blocking, but have a clear interface to the underlying interrupts.
They should do no more than allow the user to manage specific events


<a id="org523fabe"></a>

## Storage


<a id="org2399f0d"></a>

### Summary

This is also a sink of data. But, the abstraction is not as broad as an [2.2](#orgb20bc24).
Storage covers mostly what you expect, a common abstraction layer for Mass Storage,
Micro-SD Cards, Banks of EEPROMs, eMMC. The differentiating factor to [2.2](#orgb20bc24) is
the retrieval of previous information and self organizing.  

