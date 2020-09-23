---
title:  "I/O Completion Ports"
date:   2020-09-09
categories: Windows
---

I/O completion ports 是多核系统下，处理多个异步I/O请求的高效线程模型。当进程创建一个I/O completion port对象，系统创建一个队列与之关联，用来将相关请求放入队列。与接收到I/O请求创建线程相比，I/O completion port与线程池配合，可以使进程在处理并发异步请求时，更快更有效率。

可以理解为I/O completion port为I/O completion队列，线程池根据队列内容进行处理。

相关windows api包括：

1. CreateIoCompletionPort
2. GetQueuedCompletionStatus
3. PostQueuedCompletionStatus

## 参考链接

[I/O Completion Ports](https://docs.microsoft.com/en-us/windows/desktop/fileio/i-o-completion-ports)
