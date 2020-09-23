---
title:  "WTL Thunk"
date:   2020-09-09
categories: Windows
---

Thunk是一段机器码，主要有两个功能：

1. 改变调用栈;
2. 跳转到目标程序；

WTL中使用Thunk技术将GWLP_WNDPROC初始回调函数(一般为"StartWindowProc")替换为用户期望回调函数（一般为"WindowProc")。
初次接触时有几个问题可能费解：

1. WTL在何处RegisterClass

```cpp
T::GetWndClassInfo().Register()
```

2. WTL在何处存放this指针供StartWindowProc获取

```cpp
_AtlWinModule.AddCreateWndData(&this->m_thunk.cd, this);
```

3. Thunk细节

```cpp
struct _stdcallthunk
{
	DWORD   m_mov;          // mov dword ptr [esp+0x4], pThis (esp+0x4 is hWnd)
	DWORD   m_this;         //
	BYTE    m_jmp;          // jmp WndProc
	DWORD   m_relproc;      // relative jmp
	BOOL Init(
		_In_ DWORD_PTR proc,
		_In_opt_ void* pThis)
	{
		m_mov = 0x042444C7;  //C7 44 24 0C
		m_this = PtrToUlong(pThis);
		m_jmp = 0xe9;
		m_relproc = DWORD((INT_PTR)proc - ((INT_PTR)this+sizeof(_stdcallthunk)));
		// write block from data cache and
		//  flush from instruction cache
		FlushInstructionCache(GetCurrentProcess(), this, sizeof(_stdcallthunk));
		return TRUE;
	}
	//some thunks will dynamically allocate the memory for the code
	void* GetCodeAddress()
	{
		return this;
	}
	_Ret_maybenull_ _Post_writable_byte_size_(sizeof(_stdcallthunk)) void* operator new(_In_ size_t)
	{
        return __AllocStdCallThunk();
    }
    void operator delete(_In_opt_ void* pThunk)
    {
        __FreeStdCallThunk(pThunk);
    }
};
```

在 Init() 函数中这组汇编指令被初始化为下面的指令：

mov dword ptr [esp+0x4], pThis
jmp (int)proc - ((int)this+sizeof(_WndProcThunk))

它完成的功能是，用窗口类的指针 pThis 代替窗口句柄 hWnd （ esp+0x4 中放的就是 hWnd ），然后跳转到传入的 proc 函数处（ (int)proc - ((int)this+sizeof(_WndProcThunk)) 是 proc 与 thunk 之间的距离）。

## 参考链接

* [WTL中 Thunk技术本质](https://blog.csdn.net/chenyujing1234/article/details/7443084)
* [ATL Thunk机制学习](https://blog.csdn.net/tttyd/article/details/4562233)
