---
title:  "Use std classes cross dll boundary"
date:   2020-09-09
categories: CPP
---

Keep in mind one thing before you read further: My answer is coming from the point of view of writing portable code that can be used in applications made up of modules compiled under different compilers. This can include different versions or even different patch levels of the same compiler.

How can I use stl-classes in my dll-interface?

Answer: You often can't1.

Reason: The STL is a code library, not a binary library like a DLL. It does not have a single ABI that is guaranteed to be the same wherever you might use it. Indeed, STL does stand for "Standard Template Library," but a key operative word here besides Standard is Template.

The Standard defines the methods and data members each STL class is required to provide, and it defines what those methods are to do; but no more. In particular, the Standard doesn't specify how compiler writers should implement the Standard-defined functionality. Compiler writers are free to provide a implementation of an STL class that adds member functions and member variables not listed in the Standard, so long as those members which are defined in the Standard are still there and do what the Standard says.

Maybe an example is in order. The basic_string class is defined in the Standard as having certain member functions & variables. The actual definition is almost 4 pages in the Standard, but here's just a snippet of it:

```cpp
namespace std {
template<class charT, class traits = char_traits<charT>,
         class Allocator = allocator<charT> >
class basic_string {
public:
  size_type size() const;
  size_type length() const;
  size_type max_size() const;
  void resize(size_type n, charT c);
  void resize(size_type n);
  size_type capacity() const;
  void reserve(size_type res_arg = 0);
  void clear();
  bool empty() const;
};
}
```

Consider the size() and length() member functions. There is nothing in the Standard that specified member variables for holding this information. Indeed, there are no member variables defined at all, not even to hold the string itself. So how is this implemented?

The answer is, many different ways. Some compilers might use a size_t member variable to hold the size and a char* to hold the string. Another one might use a pointer to some other data store which holds that data (this might be the case in a reference-counted implementation). In fact, different versions or even patch levels of the same compiler may change these implementation details. You can't rely on them. So, MSVC 10's implementation might look like this:

```cpp
namespace std {
template<class charT, class traits = char_traits<charT>,
         class Allocator = allocator<charT> >
class basic_string {
char* m_pTheString;
};
size_t basic_string::size() const { return strlen(m_pTheString;) }
}
```

...Whereas MSVC 10 with SP1 might look like this:

```cpp
namespace std {
template<class charT, class traits = char_traits<charT>,
         class Allocator = allocator<charT> >
class basic_string {
vector<char> m_TheString;
};
size_t basic_string::size() const { return m_TheString.size(); }
}
```

I'm not saying they do look like this, I'm saying they might. The point here is the actual implementation is platform-dependent, and you really have no way of knowing what it will be anywhere else.

Now say you use MSVC10 to write a DLL that exports this class:

```cpp
class MyGizmo
{
public:
  std::string name_;
};
```

What is the sizeof(MyGizmo)?

Assuming my proposed implementations above, under MSVC10 its going to be `sizeof(char*)`, but under SP1 it will be `sizeof(vector<char>)`. If you write an application in VC10 SP1 that uses the DLL, the size of the object will look different than it actually is. The binary interface is changed.