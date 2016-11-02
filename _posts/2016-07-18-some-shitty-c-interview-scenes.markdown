---
layout: post
title: Some shitty C++ interview scenes
date: 2016-07-18 16:35:50 +07:00
type: post
published: true
status: publish
categories: general
tags: [cpp]
---

From my own experience as the interviewee, those questions is unacceptable to me,
I admit that I hate those questions, and I hate the interviewer, too.
<!--excerpt-->

### I have some C/C++ questions for you!"

My feedback: "What do you mean? Which language does your question target to? C or C++?"


The man in question: "Both, C/C++ can't be separated from each other!"


Oh man, C and C++ is 2 differences language, they are separated from each other about 30 years ago.
C isn't a subset of C++, C++ isn't a superset of C.
Except for toy examples, good C programs typically cannot be compiled with C++ compilers.


You can find some C feature which isn't support in C++, even C++14 or C++1z [here][1].
I will write a blog about something which is legal in both C and C++ but have different meaning later.
Moreover, C is a functional language, C++ is a multi-paradigm language,
these two requires difference mindset to work with.

### Ask about some undefined behavior (UB) code and expect an answer which ties to an implementation bases on their own experience

Like the title said, they ask something yield to an UB, some of those men in the questions haven't known about UB, either.
Here is some questions:

- What happens if you call <code>malloc</code> then free it by <code>delete</code>, call <code>new</code> then free by <code>free</code> from stdlib?
- Result of

```c
int i = 10;
int result = i++ + i++;
```

- Calling main recursive and/or take the address of main function in C++
- `void main`
- et cetera...

# gcc is the standard, all other compilers must follow its steps

Regarding some options or C++ extensions, which I don't remember.

# To be continued

[1]: /programming/2016/07/18/c-is-not-cpp-part1-lesser-known-feature.html

