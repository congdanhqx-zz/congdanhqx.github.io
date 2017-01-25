---
title: Binary Operator between 2 classes in C++ 11
layout: post
date: 2017-01-25 15:00:00 +07:00
author: ĐOÀN Trần Công Danh
categories: programming
tags: [cpp]
---

This article is written from [Yakk's answer](http://stackoverflow.com/a/40614334/4115625) in StackOverflow,
I gave a different answer in that question but I think Yakk's one is better.

Given 2 class `Result` and `Value`, and a binary operator is defined as `Result = Result + Value` and `Result = Value + Result`.

If your `Value` are cheap to move, it should be implemented like this:

```cpp
Result& operator+=(Result& r, const Value& v);
Result& operator+=(Result& r, Value&& v);
Result operator+=(Result&& r, const Value& v) { r += v; return std::move(r); }
Result operator+=(Result&& r, Value&& v) { r += std::move(v); return std::move(r); }

Result operator+(Result r, const Value& v) { return std::move(r) += v; }
Result operator+(Result r, Value&& v) { return std::move(r) += std::move(v); }
Result operator+(const Value& v, Result r) { return std::move(r) += v; }
Result operator+(Value&& v, Result r) { return std::move(r) += std::move(v); }
```
else, I mean if there are no advantage from moving `Value`, you can implement like this:

```cpp
Result& operator+=(Result& r, const Value& v);
Result operator+=(Result&& r, const Value& v) { r += v; return std::move(r); }

Result operator+(Result r, const Value& v) { return std::move(r) += v; }
Result operator+(const Value& v, Result r) { return std::move(r) += v; }
```

----

If you find the signature likes `Result operator+=(Result&& r, ...)` ugly, you can make it like:

```cpp
Result& operator+=(Result& r, const Value& v);
Result& operator+=(Result& r, Value&& v);

Result operator+(Result r, const Value& v) { return std::move(r += v); }
Result operator+(const Value& v, Result r) { return std::move(r += v); }
// Below function is not neccessary when moving Value is not better
Result operator+(Result r, Value&& v) { return std::move(r += std::move(v)); }
Result operator+(Value&& v, Result r) { return std::move(r += std::move(v)); }
```

Actually, this is my answer to that question :D
