---
title:  "Shared_Ptr"
date:   2020-09-09
categories: CPP
---

```cpp
template<class T>
class shared_ptr {
    ......
private:
    T * px;                         //contained pointer
    boost::detail::shared_count pn; //reference counter
};

class shared_count {
    ......
private:
    sp_counted_base * pi_;      //important
};

class sp_counted_base {
    ......
private:
    //pointer shared count
    std::atomic_int_least32_t use_count_;   
    //self(sp_counted_bast *) shared count 
    std::atomic_int_least32_t weak_count_;  
 };

 ```

## enable_shared_from_this 作用

```cpp
template<class T>
class enable_shared_from_this {};
```

```cpp
// inherited from enable_shared_from_this
template< class X, class Y, class T >
inline void sp_enable_shared_from_this(
     boost::shared_ptr<X> const * ppx, Y const * py,
     boost::enable_shared_from_this< T > const * pe ) {
    if( pe != 0 ) {
        pe->_internal_accept_owner( ppx, const_cast< Y* >( py ) );
    }
}

// not inherited from enable_shared_from_this
inline void sp_enable_shared_from_this( ... )
{
}

```
