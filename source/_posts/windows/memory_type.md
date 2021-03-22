---
title:  "Memory Type"
date:   2020-09-09
categories: Windows
---

[原地址：What is private bytes, virtual bytes, working set?](https://stackoverflow.com/questions/1984186/what-is-private-bytes-virtual-bytes-working-set)

* Private Bytes：指可执行文件申请的，不一定是实际使用的。private代表它不包括memory-mapped files（例如：共享dll）占用的内存。但是它是包括dll中的函数所分配内存的，所以Private Bytes大小的变化可能由可执行文件和共享dll引起。Private Bytes不止是物理内存，还可能page to disk或者page list(i.e. no longer in use, but not paged yet either)

* Working Set：代表进程占用的全部物理内存（RAM），但是不同于Private Bytes，它包括memory-mapped files（例如：共享dll）和其他资源，与Private Bytes相比，更加不能代表一个可执行文件使用的内存。任务管理器中的“内存使用”就是该值，最近几年造成了很多的误解。Working Set代表占用RAM中的内存大小，所以访问其中内存地址时不会有page fault。注意：虽然page list仍然在内存中，当你最小化窗口时，“内存使用”可能会突然减小。

* Virtual Bytes：代表整个进程占用的全部虚拟内存空间，包括memory-mapped files、page list以及page to disk。当系统中存在大量进程时，所有进程的Virtual Bytes的和会比机器的物理内存大出很多。