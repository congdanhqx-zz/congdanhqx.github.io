---
layout: post
title: Hey guys, don't try to be smart with swap
date: 2016-07-07 09:42:52.000000000 +07:00
type: post
published: true
status: publish
categories: programming
tags: [cpp, optimization]
---

Recently, someone told me that we should swap 2 variable without a temporary
variable like the function swap2 and swap3 of the below example because it
should be faster.

Today, I want to do some benchmark to compare those implementations to prove that
you're all wrong to out-optimize the compiler.
<!--excerpt-->

```cpp
#include <memory>

int a;
int b;
void swap1(void) 
{
  int t = a;
  a = b;
  b = t;
}

// Don't try to be smart guy
void swap2(void) 
{
  a ^= b;
  b ^= a;
  a ^= b;
}

// Don't try to be smart guy
void swap3(void)
{
  b += a;
  a = b - a;
  b -= a;
}

// Obviously, this can be done only in C++
void swap4(void)
{
  std::swap(a, b);
}
```

#include <memory>

int a;
int b;
void swap1(void) 
{
  int t = a;
  a = b;
  b = t;
}

// Don't try to be smart guy
void swap2(void) 
{
  a ^= b;
  b ^= a;
  a ^= b;
}

// Don't try to be smart guy
void swap3(void)
{
  b += a;
  a = b - a;
  b -= a;
}

// Obviously, this can be done only in C++
void swap4(void)
{
  std::swap(a, b);
}
```

However, your compilers have a flag to optimize your code (-O3 for gcc, clang,
icc, /O2 for Microsoft Visual C++).

As you can see in this website: <https://godbolt.org/g/FdAjKr>, the result code
of `swap1` and `swap4` are always the same or much better than `swap2` and
`swap3`, in detail:

- With gcc 4.6 or later, assembly of `swap1`, `swap3`, `swap4` is the same,
`swap2` is the worst.
- With gcc 4.5 or prior, assembly of `swap1` is as same as `swap4`, which is
better than `swap3`, which in turn, better than `swap2`
- With clang or icc, the output assemblies are always the same.

Of course, even if the assembly of `swap3` is the same with `swap1` and `swap4`
(in modern compiler), it's not recommended way to did this because sum of
`a + b` may overflow `std::numeric_limits&lt;int&gt;::max()`, thus result an
undefined behavior.

Hence, I would like to recommend you write code which uses temporary variable
like `swap1` did or, for better and smarter code, uses `std::swap` if you are
writing C++ code.

It's smarter to use whatever compiler give you.
