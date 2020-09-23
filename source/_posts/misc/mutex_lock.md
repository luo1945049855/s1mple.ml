---
title:  "Mutex vs Lock"
date:   2020-09-09
categories: Misc
---

## mutex

mutex提供exclusive一种lock

1. lock、try_lock、unlock

shared_mutex提供shared以及exclusive两种lock：

1. `lock_shared、try_lock_shared、unlock_shared`
2. `lock、try_lock、unlock`

## lock

* `unique_lock<mutex>`提供lock、try_lock、unlock接口，实为调用mutex/shared_mutex的exclusive接口。
* `shared_lock<mutex>`提供lock、try_lock、unlock接口，实为调用shared_mutex的shared接口。
* unique_lock和shared_lock都是只能move不能copy的。
* unique_lock和shared_lock可以配合shared_mutex实现read-write lock。