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

```cpp
template<typename Container, typename Index>
decltype(auto) authAndAccess(Container& c, Index i)
authAndAccess(std::vector<int>{1, 2, 3}, 0); // compile error.

template<typename Container, typename Index>
decltype(auto) authAndAccess(const Container& c, Index i)
authAndAccess(std::vector<int>{1, 2, 3}, 0); // ok.
```

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

### Perfect Forwarding

* Function templates that take arbitrary arguments
  and forward them to other functions such that
  the target functions receive `exactly the same` arguments.

* RValue references should be unconditionally cast to rvalues, because they're always bound to rvalues.
  Universal references should be conditionally case to rvalues, because they're only sometimes bound to rvalues.

* Do the same thing for rvalue references and universal references
  being returned from functions that return by value.

* Never apply std::move or std::forward to local objects
  if they would otherwise be eligible for the return value optimization.

* `return value optimization(RVO)`:
  the type of the local object is the same as that returned by the function.
  the local object is what's being returned.
* If the conditions for the `RVO are met`,
  but compilers choose `not to perform copy elision`,
  the object being returned must be treated as an `rvalue`.

* Functions taking universal references are the greediest functions in C++.
* When a template instantiation and non-template function are equally good matches for a function call,
  the normal function is preferred.

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

## Lambda Expression

* std::function object typically uses more memory that auto-declared object.
  std::function object is almost certain to be slower that calling it via an auto-declared object.

## Function's `reference qualifiers`

* They make it possible to limit use of a member function to lvalues only or rvalues only.

```cpp
class Widget {
public:
    void doWork() &;  // *this is an lvalue
    void doWork() &&; // *this is an rvalue
};
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

## enum class (Scoped Enum)

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

## Special member function generation

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
