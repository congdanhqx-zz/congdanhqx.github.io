---
title: Get type of member function from its parameter
layout: post
date: 2016-11-05 15:47:00 +07:00
author: ĐOÀN Trần Công Danh
categories: programming
tags: [cpp, template]
---

It's my [answer on stack overflow][1]

```cpp
#include <type_traits>
#include <tuple>
#include <iostream>

template<typename T, typename... Args>
struct MethodInfo {
    template<typename Ret>
    static auto get(Ret(T::*)(Args...)) -> Ret(T::*)(Args...);
};

struct Foo {
    int foo(int);
    int foo(int, int);
};

int main() {  
    static_assert(std::is_same<
                        int(Foo::*)(int), 
                        decltype(MethodInfo<Foo, int>::get(&Foo::foo))
                          >::value, "");
}
```

[1]: //stackoverflow.com/a/40434788/4115625
