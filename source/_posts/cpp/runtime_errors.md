---
title:  "Runtime Error"
date:   2020-09-09
categories: CPP
---

## Pure Virtual on Call

1. Reason

They can result if you try to make a virtual function call from a constructor or destructor. Since you can't make a virtual function call from a `constructor` or `destructor` (the derived class object hasn't been constructed or has already been destroyed), it calls the base class version, which in the case of a pure virtual function, doesn't exist.
