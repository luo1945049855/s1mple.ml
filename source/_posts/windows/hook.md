---
title:  "Hook"
date:   2020-09-09
categories: Windows
---

```cpp
HHOOK SetWindowsHookExA(
  int       idHook,
  HOOKPROC  lpfn,
  HINSTANCE hmod,
  DWORD     dwThreadId
);
BOOL UnhookWindowsHookEx(
  HHOOK hhk
);
```

## 注意

* 当dwThreadId为0或为其它进程创建时，lpfn必须在一个dll中。
* 当dwThreadId为0时，钩子关联所有线程。
* hmod为lpfn所在dll句柄。当dwThreadId为当前进程创建或lpfn在当前进程代码中时，hmod必须为NULL。

## 参考链接

* [setwindowshookexa](https://docs.microsoft.com/en-us/windows/desktop/api/winuser/nf-winuser-setwindowshookexa)

* [using-hooks](https://docs.microsoft.com/en-us/windows/desktop/winmsg/using-hooks)
