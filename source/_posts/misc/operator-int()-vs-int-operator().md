---
title:  "operator int() vs int operator()"
date:   2020-09-09
categories: Misc
---

```cpp
struct A
{
    int operator()
    {
        return 0;
    }

    // operator() override turn an object into a `function object`.

    operator int()
    {
        return 0;
    }

    // convertion operator construct a int object by an object.
};

```
