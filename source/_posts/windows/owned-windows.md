---
title:  "Owned Windows"
date:   2020-09-09
categories: Windows
---

An overlapped or pop-up window can be owned by another overlapped or pop-up window. Being owned places several constraints on a window.

An owned window is always above its owner in the z-order.
The system automatically destroys an owned window when its owner is destroyed.
An owned window is hidden when its owner is minimized.
Only an overlapped or pop-up window can be an owner window; a child window cannot be an owner window. An application creates an owned window by specifying the owner's window handle as the hwndParent parameter of CreateWindowEx when it creates a window with the WS_OVERLAPPED or WS_POPUP style. The hwndParent parameter must identify an overlapped or pop-up window. If hwndParent identifies a child window, the system assigns ownership to the top-level parent window of the child window. After creating an owned window, an application cannot transfer ownership of the window to another window.

Dialog boxes and message boxes are owned windows by default. An application specifies the owner window when calling a function that creates a dialog box or message box.

An application can use the GetWindow function with the GW_OWNER flag to retrieve a handle to a window's owner.

## Reference

1. [Window Features](https://docs.microsoft.com/en-gb/windows/win32/winmsg/window-features)