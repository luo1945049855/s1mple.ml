---
title:  "operator int() vs int operator()"
date:   2020-09-09
categories: CPP
---

```cpp
struct A
{
    // operator() override turn an object into a `function object`.

    int operator()
    {
        return 0;
    }

    // convertion operator construct a int object by an object.

    operator int()
    {
        return 0;
    }
};

```
