---
title:  "Memory Leak"
date:   2021-02-20
categories: Windows
---

## Using Performance Monitor to Find a User-Mode Memory Leak

1. Launch `perfmon.exe`. Add the following counters:

    * Process-->`Private Bytes` which indicates the `total amount of memory that a process has allocated`, not including memory shared with other processes.

    * Process-->`Virtual Bytes` which indicates `the current size of the virtual address space that the process is using`.

    Change the update time to 600 seconds to capture a graph of leak over time. You might also want to log the data to a file for later examination.

## UMDH(User-Mode Dump Heap) + GFlags

1. Enable `Create user mode stack trace database` by GFlags.exe under `Image File` tab.

   While it's possible to turn on the user stack database with the `!gflag` debugging extension.

2. You must compare log files recorded at different time to determine which allocation are growing.

    ```bat
    :: record the start state.
    umdh -p:pid -f:log1.txt

    :: -------------some operations--------------

    :: record the end state.
    umdh -p:pid -f:log2.txt

    :: compare 
    :: you may also include the -d option,
    :: which converts the byte and allocation
    :: counts from hexadecimal to decimal.

    umdh log1.txt log2.txt > logCompare.txt

    ```

3. Open the logCompare.txt file. Its contents resemble the following:

    ```txt
    + 5320 ( f110 - 9df0) 3a allocs BackTrace00B53 
    Total increase == 5320  
    ```

    * 0xf110 bytes allocated by BackTrace00B53 according to records in log2.txt.
    * 0x9d50 bytes allocated by BackTrace00B53 according to records in log1.txt.
    * 0x5320 bytes is the increased bytes allocated by BackTrace00B53 during log1 to log2.

4. To determine what is in that backtrace, open one of the original log files(for example, log2.txt) and search for "BackTrace00B53". The results are similar to this data:

    ```txt
    00005320 bytes in 0x14 allocations (@ 0x00000428) by: BackTrace00B53
    ntdll!RtlDebugAllocateHeap+0x000000FD
    ntdll!RtlAllocateHeapSlowly+0x0000005A
    ntdll!RtlAllocateHeap+0x00000808
    MyApp!_heap_alloc_base+0x00000069
    MyApp!_heap_alloc_dbg+0x000001A2
    MyApp!_nh_malloc_dbg+0x00000023
    MyApp!_nh_malloc+0x00000016
    MyApp!operator new+0x0000000E
    MyApp!DisplayMyGraphics+0x0000001E
    MyApp!main+0x0000002C
    MyApp!mainCRTStartup+0x000000FC
    KERNEL32!BaseProcessStart+0x0000003D
    ```

    * 0x5320 bytes allocated from 0x14(decimal 20) separate allocations of 0x428(decimal 1064) bytes each.

## Reference

* [Using Performance Monitor to Find a User-Mode Memory Leak](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/using-performance-monitor-to-find-a-user-mode-memory-leak)
* [Debugging Chromium on Windows](https://www.chromium.org/developers/how-tos/debugging-on-windows)
* [Using UMDH to Find a User-Mode Memory Leak](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/using-umdh-to-find-a-user-mode-memory-leak)
