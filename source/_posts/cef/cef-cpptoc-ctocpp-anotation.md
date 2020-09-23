---
title:  "CPP-C"
date:   2020-09-09
categories: CEF
---

## cpp to c关键代码

```cpp
template<class ClassName, class BaseName, class StructName>
class CefCppToC : public CefBase {
public:
  static StructName* Wrap(CefRefPtr<BaseName> c) {
    if (!c.get())
      return NULL;
    ClassName* wrapper = new ClassName();
    wrapper->wrapper_struct_.object_ = c.get();
    wrapper->AddRef();
    return wrapper->GetStruct();
  }

protected:
  CefCppToC() {
    wrapper_struct_.type_ = kWrapperType;
    wrapper_struct_.wrapper_ = this;
    memset(GetStruct(), 0, sizeof(StructName));

    cef_base_t* base = reinterpret_cast<cef_base_t*>(GetStruct());
    base->size = sizeof(StructName);
    base->add_ref = struct_add_ref;
    base->release = struct_release;
    base->has_one_ref = struct_has_one_ref;

#ifndef NDEBUG
    base::AtomicRefCountInc(&DebugObjCt);
#endif
  }
 private:
  struct WrapperStruct {
    CefWrapperType type_;
    BaseName* object_;
    CefCppToC<ClassName, BaseName, StructName>* wrapper_;
    StructName struct_;
  };
};
```

### 模板实例化

```cpp
class CefAppCppToC
    : public CefCppToC <CefAppCppToC, CefApp, cef_app_t>
{
CefAppCppToC::CefAppCppToC() {
  GetStruct()->on_before_command_line_processing =
      app_on_before_command_line_processing;
  GetStruct()->on_register_custom_schemes = app_on_register_custom_schemes;
  GetStruct()->get_resource_bundle_handler = app_get_resource_bundle_handler;
  GetStruct()->get_browser_process_handler = app_get_browser_process_handler;
  GetStruct()->get_render_process_handler = app_get_render_process_handler;
}
};
```

### 关键点

* 模板参数分别为：派生类，基类，C结构体。
* CefCppToC中存在C结构体WrapperStruct对象。
* WrapperStruct中BaseName* object_保存cpp对象指针。
* WrapperStruct中StructName struct_保存c对象。
* CefCppToC::AddRef调用object_::AddRef。
* CefCppToC构造函数初始化C结构体cef_base_t成员，派生类初始化派生结构体中函数指针（如：CefAppCppToC初始化cef_app_t中函数指针)。

## c to cpp关键代码

```cpp
template <class ClassName, class BaseName, class StructName>
CefRefPtr<BaseName>
    CefCToCpp <ClassName, BaseName, StructName> ::Wrap(StructName* s) {
  if (!s)
    return NULL;
  WrapperStruct* wrapperStruct = new WrapperStruct;
  wrapperStruct->type_ = kWrapperType;
  wrapperStruct->struct_ = s;
  CefRefPtr<BaseName> wrapperPtr(&wrapperStruct->wrapper_);
  wrapperStruct->wrapper_.UnderlyingRelease();
  return wrapperPtr;
}
template <class ClassName, class BaseName, class StructName>
struct CefCToCpp<ClassName,BaseName,StructName>::WrapperStruct {
  CefWrapperType type_;
  StructName* struct_;
  ClassName wrapper_;
};
StructName* GetStruct() const {
WrapperStruct* wrapperStruct = GetWrapperStruct(this);
DCHECK_EQ(kWrapperType, wrapperStruct->type_);
return wrapperStruct->struct_;
}
template <class ClassName, class BaseName, class StructName>
typename CefCToCpp<ClassName, BaseName, StructName>::WrapperStruct*
    CefCToCpp<ClassName, BaseName, StructName>::GetWrapperStruct(
        const BaseName* obj) {
  return reinterpret_cast<WrapperStruct*>(
      reinterpret_cast<char*>(const_cast<BaseName*>(obj)) -
      (sizeof(WrapperStruct) - sizeof(ClassName)));
}
```

### 关键点

* 模板参数与CppToC类似。
* CefCToCpp::WrapperStruct中StructName* struct_保存c结构体地址。
* CefCToCpp::WrapperStruct中wrapper保存cpp对象。
* CefCToCpp::**GetWrapperStruct**为**桥梁**函数，起到关键作用。该函数首先根据数据成员和结构体的内存偏移得到CefCToCpp::WrapperStruct，从而得到c结构体指针struct_。
* cef_base_t * struct内部函数使用类似上述的技术，先得到CefCppToC::WrapperStruct，从而得到原cpp对象object_。