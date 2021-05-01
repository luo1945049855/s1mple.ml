---
title:  "Effective Modern CPP"
date:   2021-04-30
categories: CPP
---

## RValue && LValue

* `RValues` correspond to temporary objects
  `Lvalues` correspond to objects you can refer to, either by name or by following a pointer or lvalue reference.

* `LValues` usually can be taken address, while `RValues` can't.

* The type of an expression is independent of whether the expression is an lvalue or an rvalue.
  A parameter of rvalue reference type, itself is an lvalue.

* `RValues` can't bind to lvalue references, unless they're lvalue-references-to-const.

  ```c++
  template<typename Container, typename Index>
  decltype(auto) authAndAccess(Container& c, Index i)
  authAndAccess(std::vector<int>{1, 2, 3}, 0); // compile error.

  template<typename Container, typename Index>
  decltype(auto) authAndAccess(const Container& c, Index i)
  authAndAccess(std::vector<int>{1, 2, 3}, 0); // ok.
  ```

* Reference Collapsing:
If either reference is an lvalue reference, the result is an lvalue reference.
Otherwise(i.e.,if both are rvalue reference) the result is an rvalue reference.

* Reference Collapsing occurs in four contexts:

1. `template` instantiation.

2. generation for `auto` variables.

3. generation and use of `typedef`s and alia declarations.

4. use of `decltype`.

## Smart Pointers

* `control block` contains:
reference count;
weak count;
a copy of custom deleter;
a copy of custom allocator;

* constructing more than one std::shared_ptr from a single raw pointer, undefined behavior.
  `std::enable_shared_from_this<T>` use
  `The Curiously Recurring Template Pattern(CRTP)` can solve this problem.

* std::shared_ptr can be created from a std::unique_ptr.

* `std::shared_ptr<T> sp(new T[size], delete[])` isn't recommended;

* std::make_shared allocates `a single chunk of memory` to hold both the Widget object and the control block.

* std::unique_ptr<T, deleter> the type of deleter is part of the type of the smart pointer.
  pointed-to types must be complete when compiler-generated special functions.

## Template

* SFINAE(Substitution Failure Is Not An Error) && std::enable_if
* std::is_integral
* std::is_same
* std::decay
* std::is_base_of
* static_assert
* std::is_constructible

```cpp
class Person {
public:
    template <
        typename T,
        typename = std::enable_if_t<
          !std::is_base_of<Person, std::decay_t<T>>::value && 
          !std::is_integral<std::remove_reference_t<T>>::value
        >
    >
    explicit Person(T&& n) : name(std::forward<T>(n)) {
        static_assert(std::is_constructible<std::string, T>::value, 
        "Parameter n can't be used to construct a std::string");
    }

    explicit Person(int idx)
    : name(nameFromIdx(idx))
    {...}
private:
  std::string name;
};
```

* Arrays and Functions can decay into pointers.

```cpp
void someFunc(int, double);

template <typename T>
void f1(T param);

template <typename T>
void f2(T& param);

f1(someFunc); // param deduced as ptr-to-func. void(*)(int, double);
f2(someFunc); // param deduced as ref-to-func. void(&)(int, double);
```

* Function can't declare parameters that are truly arrays,
  they can declare parameters that are `reference to array`
  which interestingly enables creation of a template that
  deduces `the number of elements` that an an array contains:

```cpp
template <typename T, std::size_t N>
constexpr std::size_t arraySize(T (&)[N]) noexcept
{
    return N;
}
```

* Alias Templates

```cpp
typedef void (*FP)(int, const std::string&);    // typedef
using FP = void (*) (int, const std::string&);  // alias declaration

template <typename T>
using MyAllocList = std::list<T, MyAlloc<T>>;
```

## Perfect Forwarding

* Function templates that take arbitrary arguments
  and forward them to other functions such that
  the target functions receive `exactly the same` arguments.

* RValue references should be unconditionally cast to rvalues, because they're always bound to rvalues.
  Universal references should be conditionally case to rvalues, because they're only sometimes bound to rvalues.

* Do the same thing for rvalue references and universal references being returned from functions that return by value.

* Never apply `std::move` or `std::forward` to local objects if they would otherwise be eligible for the `return value optimization`.

* Functions taking `universal references` are the greediest functions in C++.
* When a `template instantiation` and `non-template function` are equally good matches for a function call, the normal function is preferred.

* The kind of arguments that can't be perfect-forwarded:
  
  * Braced initializers.

    A braced initializer to a function template parameter that's not declared to be a `std::initializer_list` is decreed to be, as the Standard puts it, a "non-deduced context."
    Compilers are forbidden from deducing a type for the expression {1, 2, 3}, because fwd's parameter isn't declared to be a `std::initializer_list`.

    ```cpp
    void f(const std::vector<int>& v);

    template <typename T>
    void fwd(T& v)
    {
      f(std::forward<T>(v));
    }

    f ({1, 2, 3});  // ok.
    fwd({1, 2, 3}); // error.
    ```

  * 0 or NULL as null pointers.

    0 and NULL will be deduced to integral type(typically int).

  * declaration-only integral `static const` data members.

    fwd's parameter is a universal reference, and references, in the code generated by compilers, are usually treated like pointers.
    In the program's underlying binary code(and on the hardware), `pointers and references` are essentially the same thing.

    ```cpp
    class Widget {
    public:
      static const std::size_t MinVals = 28;
    };
    std::vector<int> widgetData;
    widgetData.reserve(Widget::MinVals); // ok.

    f(Widget::MinVals);   // ok.
    fwd(Widget::MinVals); // error. link error.
    ```

  * overloaded function names and template names.

    void f(int (*pf)(int));
    void f(int pf(int));    // same to above.

    ```cpp
    int processVal(int value);
    int processVal(int value, int priority);
    fwd(processVal); // error! which processVal.
    ```

  * Bitfields

    fwd's parameter is a reference, h.totalLength is a non-const bitfield.

    The C++ Standard: "A non-const reference shall not be bound to a bit-field." But reference-to-const is allowed.

    There's no way to create a pointer to arbitrary bits(C++ dictates that the smallest thing you can point to is a char).

    ```cpp
    struct IPv4Helper {
      std::uint32_t version: 4,
                    IHL: 4,
                    DSCP: 6,
                    ECN: 2,
                    totalLength: 16;
    };
    
    IPv4Helper h;
    f(h.totalLength); // ok.
    fwd(h.totalLength); // error.
    ```

## Uniform Initialization

* When the initializer for an `auto-declared` variable is `enclosed in braces`, the deduced type is a `std::initializer_list`.

* Function calls using the braced initialization syntax `strongly prefer` the overloads taking `std::initializer_list`.
* `Narrowing Conversion` are prohibited inside braced initializers.

```cpp
class Widget {
public:
    Widget(int i, bool b);
    Widget(int i, double d);
    Widget(std::initializer_list<int> il);
};

Widget w1{10, true}; // calls std::initializer_lsit<int>
                     // (10 and true convert to int)
Widget w2{10, 5.0};  // error! 
                     // narrowing conversions(5.0 can't be exactly represented by int).
```

* The only real difference between `auto` and `template` type deduction is that `auto` assumes that a `braced initializer` represents a `std::initializer_list`, but template type deduction doesn't.

```cpp
auto x = {1, 2, 3.0}; // error! can't deduce T for std::initializer_list<T> 
                      // because int and double are different types
auto x1 = {11, 23, 9}; // x's type is std::initializer_list<int>

template <typename T>
void f(T param);
f({11, 23, 9}); // error! can't deduce type for T
```

* Empty braces mean no arguments, not an empty std::initializer_list.
  Put empty braces inside `parentheses` or `braces` demarcating what you're passing.

```cpp
Widget w1{};   // calls default ctor.
Widget w2({}); // std::initializer_list with empty list.
Widget w3{{}}; // std::initializer_list with empty list.
```

* `std::vector<number type>` use brace initializer

```cpp
std::vector<int> v1(10, 20); // use non-std::initializer_list ctor:
                             // 10-element all have value of 20.
std::vector<int> v2{10, 20}; // use std::initializer_list ctor:
                             // 2-element values are 10, 20.
```

## Special Member Function Generation

C++98:

* These functions are generated only if they're needed,
  i.e., if some code uses them without their being expressly declared in the class.
* Generated special member functions are implicitly `public` and `inline` and
  `nonvirtual`(unless destructor in a derived class inheriting from a base class with a virtual destructor).

* default constructor: no constructor at all(prevent constructor arguments are required)

* destructor: noexcept by default. virtual only if base class destructor is virtual.

  `delete` of `typeid` on a derived class object through `a base class pointer` or `reference` yield undefined or misleading results.

* copy constructor:
  deleted if class declares a move operation.
  generation of this function in a class with user-declared `copy assignment operator` or `destructor` is deprecated.

* copy assignment:
  deleted if class declares a move operation.
  generation of this function in a class with user-declared `copy constructor` or `destructor` is deprecated.

C++11:

* memberwise move consits of move operations on `data members`
  and `base classes` that support move operations, but a copy operation for those that don't.

* The two move operations, if you declare either, that prevents compilers from generating the other.
* Move operations won't be generated for any class that explicitly declared a copy operation.
* Declaring a move operation in a class causes compilers to disable the copy operations.

* User-defined destructor has no impact on compiler's generated copy operations,
  because that will break too much legacy code.
* User-defined destructor prevents compiler from generating move operations.

* Template Member Functions don't preventing compiler from generating the special member functions.

* move constructor: no copy, no move, no destructor.
* move assignment: no copy, no move, no destructor.

## Lambda Expression

* `std::function` object typically uses more memory that auto-declared object.
  `std::function` object is almost certain to be slower that calling it via an auto-declared object.
* `std::bind` all arguments passed to `bind objects` are passed by reference using `perfect-forwarding`.

* Each lambda causes compilers to generate a unique `closure class`(compile time).

  By default, the `operator()` member function inside the closure class generated from a lambda is `const`.
  If the lambda were declared `mutable`, `operator()` in its closure class would not be declared `const`.

  `closure object`(runtime) may generally be copied, so it's possible to have multiple closures of a closure type corresponding to a single lambda.

* std::all_of
  
  ```cpp
  std::all_of(
    std::begin(container), std::end(container), 
      [&](const ConstElem& value) { reutn value % divisor == 0; });
  ```

* init capture(generalized lambda capture)

  ```cpp
  auto func = [pw = std::move(pw)]() {...}
  ```

* C++14 availing ourselves of the standard suffixes for seconds(s), milliseconds(ms), hours(h). These suffixes are implemented in the `std::literals` namespace.

## Concurrency API

* Hardware threads: CPU threads.
  Software threads: OS threads(System threads).
  std::threads: objects in a C++ process.

  When there are more ready-to-run `software threads` than `hardware threads`, the `thread scheduler(OS)` time-slices the software threads on the hardware.

  `Context Switches` increase the overall thread management overhead of the system.

  They can be particularly costly when the `hardware thread` on which a `software thread` is scheduled
  is on a `different core` that was the case for the software thread during its `last time-slice` because of `CPU caches`.

* `std::future` can get return value of an async task, even `exception` can be get access to.

  `std::launch::deferred` launch policy means that f may run only when `get` or `wait` is called on the future returned by `std::async`.

  ```cpp
  fut = std::async(doAsyncWork); // doAsyncWork may throw.

  if (fut.wait_for(0s) == 
      std::future_status::deferred)) {} // check to see if task was deferred...
  ```

* If you try to create threads more than the system can provide, a `std::system_error` exception is thrown.

* Invoking `join` or `detach` on an unjoinable thread yields `undefined behavior`.

* `shared state` is the place where callee's result is stored.

  caller(std::future) <------[shared state]--------- callee (std::promise)

* The destructor for the `last future` referring to a `shared state` for a `non-deferred task` launched via `std::async` blocks until the task completes.

  For asynchronously running tasks, this is akin to an implicit `detach` on the underlying thread.
  For `deferred tasks` for which this is the final future, it means that the deferred task will never run.

* A `std::packaged_task` object prepares a function(or other callable object) for asynchronous execution by wrapping it such that its result is put into a `shared state`.
  The future created by `std::packaged_task` doesn't satisfy above conditions.

  ```cpp
  int calcValue();

  std::packaged_task<int()> pt(calcValue);

  auto fut = pt.get_future();

  std::thread t(std::move(pt)); // it's upto thread, not future.

  std::async(std::move(pt)); // no reason to don't use std::async directly.
  ```

* `std::promise` and `std::future` can be used for event communication.

  ```cpp
  std::promise<void> p;

  void react();

  void detect()
  {
    std::thread t([]
    {
      p.get_future().wait();
      react();
    })

    ... // some work prior to call to react.

    p.set_value();

    t.jon();
  }
  ```

  ```cpp
  // suspend then unsuspend not just one reacting task, but many.
  // std::shared_future instead of std::future.
  std::promise<void> p;

  void detect()
  {
    auto sf = p.get_future().share();

    std::vector<std::thread> vt;

    for (int i = 0; i < threadsToRun; ++i)
    {
      vt.emplace_back([sf] { sf.wait(); react(); });
    }

    ... // some work prior to call to react.

    p.set_value();

    ...

    for (auto&t : vt)
    {
      t.join();
    }
  }
  ```

* `std::atomic` implemented using special machine instructions that are more efficient than `mutex`.

* `volatile` for special memory. `std::atomic` for concurrency.

  ```cpp
  std::atomic<int> ac(0);
  volatile int vc(0);

  /*------thread1-------*/      /*------thread2-------*/      
         ++ac;                        ++ac;
         ++vc;                        ++vc;
  ```

  Each increment consists of `reading vc's value`, `incrementing the value`, `writing the result back`.
  But these operations are not guaranteed to proceed atomically.

* Generally, compilers are permitted to reorder unrelated assignments:

  Even if compilers don't reorder them, the underlying `hardware` might do it.

  `std::atomic` using `sequential consistency` imposes restrictions on reorder, no code that precedes a `write` of a `std::atomic` may take place afterwards.

  ```cpp
  a = b;
  x = y;
  // compiler may generally reorder them as:
  x = y;
  a = b;
  ```

* `volatile` can't do above thing. but it can tell compilers that we're dealing with `special memory`,
  "Don't perform any optimizations on operations on this memory".

  ```cpp
  //##########1
  x = 10; // eliminated.
  x = 20;

  //##########2
  auto y = x;
  y = x;  // eliminated.
  x = 10; // eliminated.
  x = 20;

  //#########3
  std::atomic<int> y(x.load());
  y.store(x.load());

  // "optimize"
  register = x.load();
  std::atomic<int> y(register);
  y.store(register);
  ```

## Exception

* Functions offering the strong exception safety guarantee(i.e., the `strong guarantee`) assure callers that if an exception arises, the state of the program remains as it was prior to the call.

* Applying `noexcept` to functions that won't produce exceptions: it permits compilers to generate better object code:

  In a `noexcept` function, optimizers needn't keep the runtime stack in an unwindalbe state if an exception would propagate out of the function.

  nor must they ensure that objects in a `noexcept` function are destroyed in the inverse order of construction should an exception leave the function.

* `noexcept(expression)` result is true if the potential exception of the expression is empty.

* Destructor is implicitly `noexcept` unless a data member of the class is of a type that expressly states that its destructor may emit exceptions(e.g., declares it "noexcept(false)").

* If an exception propagates out of a thread's primary function,
  or a `noexcept` specification is violated, local objects may not be destroyed,
  `std::abort` or an exit function(i.e., `std::_Exit, std::exit, std::quick_exit`) is called.

```cpp
template <class T, size_t N>
void swap(T (&a)[N], T (&b)[N]) noexcept(noexcept(swap(*a, *b)));
```

## auto && decltype()

* C++11 `trailing return type` syntax:

```cpp
template <typename Container, typename Index>
auto authAndAccess(Container& c, Index i) ->decltype(c[i])
{
    authenticateUser();
    return c[i];
}
```

* C++14 permits `auto` to indicate that a function's return type.
  C++14 lambdas may use `auto` in parameter declarations.
  However, these uses of `auto` employ `template type deduction` not `auto` type deduction.
* Template Type Deduction, the reference-ness of an initializing expression is ignored.
  `auto` return type deduction will strip off the reference

```cpp
template <typename Container, typename Index>
auto authAndAccess(Container& c, Index i)
{
    authenticateUser();
    return c[i];
}
```

```cpp
std::deque<int> d;
...
autoAndAccess(d, 5) = 10; // reference striped. error.
```

* `decltype(auto)` `auto` specifies that the type is to be deduced, and `decltype` says that `decltype` rules should be used during the deduction.

  `decltype(auto)` isn't limited to function return types but also `declaring variables`.

```cpp
template <typename Container, typename Index>
decltype(auto) authAndAccess(Container& c, Index i)
{
    authenticateUser();
    return c[i];
}
std::deque<int> d;
...
autoAndAccess(d, 5) = 10; // ok.

// ************************ 
Widget w;
const Widget& cw = w;
auto myWidget1 = cw; // auto type deduction: myWidget1's type is Widget
decltype(auto) myWidget2 = cw; // decltype deduction: myWidget2's type is const Widget&
```

* `decltype` to a name yields the type `T` for that name.
  `decltype` to a lvalue expression other than a name yields `T&`.

```cpp
int x = 0;
decltype(x) is int.
decltype((x)) is int&.
```

## emplace_back

* emplace_back; emplace_front; emplace;
  emplace_hint(associative container); emplace_after(std::forward_list)

* `Insertion functions` take objects to be inserted.
  `Emplacement functions` task constructor arguments for obejcts to be inserted which avoid the creation and destruction of `temporary objects`.

* copy initialization && direct initialization

  direct initialization can use `explicit` constructor.

  emplacement functions use direct initialization, which means they may use `explicit` constructors.

  ```cpp
  std::regex r1 = nullptr; // error! copy initialization
  std::regex r2(nullptr);  // compiles. direct initialization
  ```

## enum class (scoped enum)

* `enum class Color: std::uint32_t { black, white, red }`

* By default, the underlyng type for scoped enum is `int`. `std::underlying_type_t<Color>`.
  Unscoped enums have no default underlying type.

* Scoped enums may be forward-declared, because the underlying type for them is always known.
  Unscoped enums may be forward-declared only if their declaration specifies an underlying type.

## =delete

* Any functions may be deleted.
* Deleted functions can prevent use of template instantiations that should be disabled.
  It's not possible to give a member function template specialization
  a different access level from that of the main template.

```cpp
bool isLucky(int number);
bool isLucky(char) = delete; // reject chars

template <typename T>
void processPointer(T* ptr);
template<>
void processPointer<void>(void*) = delete;

class Widget {
public:
...

    template <typename T>
    void processPointer(T* ptr) {...}

private:
    template <>
    void processPointer<void>(void*);
};
```

## constexpr

* `constexpr` indicated a value that's not only constant, it's known during compilation.
* `constexpr functions` produce compile-time constants when they are called with compile-time constants.
  if they're called with values not known until runtime, they produce runtime values.

* I/O statements are generally not permitted in `constexpr` functions.

* The conditional "?:" operator can be used in place of if-else statements.
  Recursion can be used instead of loops.

* `constexpr functions` are limited to taking and returning `literal types`.

  In C++11, all built-in types except `void` qualify.
  In C++11, `constexpr member functions` are implicitly const;
  In C++14, the restrictions on `constexpr` functions are substantially looser.

  user-defined types may be literal too, because constructors and other member functions may be constexpr.

```cpp
constexpr int pow(int base, int exp) noexcept
{
    return (exp == 0 ? 1 : base * pow(base, exp - 1));
}

// C++14

constexpr int pow(int base, int exp) noexcept
{
    auto result = 1;
    for (int i = 0; i < exp; ++i)
        result *= base;
    return result;
}

// user-defined literal
// In C++11, setX, setY aren't const and they have void return types.
// In C++14, setX, setY can be constexpr.

class Point 
{
public:  
constexpr Point(double xVal = 0, double yVal = 0) noexcept  : x(xVal), y(yVal)  {} 
constexpr double xValue() const noexcept { return x; }  
constexpr double yValue() const noexcept { return y; }  

#if __cplusplus==201402L
constexpr void setX(double newX) noexcept { x = newX; }  
constexpr void setY(double newY) noexcept { y = newY; }
#elif __cplusplus==201103L
void setX(double newX) noexcept { x = newX; }  
void setY(double newY) noexcept { y = newY; }
#endif

void 
private:  
double x, y;
};
```

## mutable

* `const member functions` can change `mutable` data members.

```cpp
class Polynomial {
public:
    using RootsType = std::vector<double>;

    RootsType roots() const 
    {
        if (!rootAreValid)
        {
            rootsAreValid = true;
        }
        return rootsVals;
    }

private:
    mutable bool rootsAreValid {false};
    mutable RootsType rootsVals {};
};
```

## Function's `Reference Qualifiers`

* They make it possible to limit use of a member function to lvalues only or rvalues only.

```cpp
class Widget {
public:
    void doWork() &;  // *this is an lvalue
    void doWork() &&; // *this is an rvalue
};
```

## RVO(Return Value Optimization)

* the type of the local object is the same as that returned by the function.
  the local object is what's being returned.

* If the conditions for the `RVO are met`, but compilers choose `not to perform copy elision`,
  the object being returned must be treated as an `rvalue`.

## SSO(Small String Optimization)

* "small" strings (e.g., those with a capacity of no more than 15 characters)
  are stored in a buffer within the `std::string` object. no heap-allocated storage is used.
