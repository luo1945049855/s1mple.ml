---
title:  "Warnings"
date:   2020-09-09
categories: CPP
---

## c4251

当导出类中包含未导出类的数据成员时出现，例如：

```cpp
template <typename T>
class TemplateClass
{
public:

};

class --declspec(dllexport) ExportedClass 
{
public:

private:
    TemplateClass<int> m_member;
};

```

## 解决

Pimpl(Pointer to implementation)模式
