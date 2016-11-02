---
layout: post
title: 'C is not C++. Part 1: Lesser-known C features'
date: 2016-07-18 14:59:55.000000000 +07:00
type: post
published: true
status: publish
categories: programming
tags: [c-language, cpp]
---

These features are the less known features of C language, and they're also
part of the difference between C and C++
<!--excerpt-->

# Variable Length Array(VLA)

Defined at section 6.7.5.2 of [N1256][1]

Currently, VLA is supportted by many major C compilers like gcc, clang.
It's supported as an extension of C++ by some major C++ compiler like g++, clang++.
It isn't supported by MSVC++ 
(For reference: <https://msdn.microsoft.com/en-us/library/02y9a5ye.aspx>
and <https://msdn.microsoft.com/en-us/library/zb1574zs.aspx>)

Example (copied for gcc documentation):

```c
FILE *
concat_fopen (char *s1, char *s2, char *mode)
{
  char str[strlen (s1) + strlen (s2) + 1];
  strcpy (str, s1);
  strcat (str, s2);
  return fopen (str, mode);
}
void
tester (int len, char data[len][len])
{
  /* ... */
}
```

For more information, read it in: [Variable Length Array][2]

# Flexible array member of struct

As defined in item 16 of section 6.7.2.1 of C99 standard (it's the item #15 in
C99 draft [here][3], it's item #16 in [N1256][1]).

> As a special case, the last element of a structure with more than one named
member may have an incomplete array type; this is called a flexible array member.
In most situations, the flexible array member is ignored. In particular, the
size of the structure is as if the flexible array member were omitted except
that it may have more trailing padding than the omission would imply.
However, when a . (or ->;) operator has a left operand that is (a pointer to)
a structure with a flexible array member and the right operand names that member,
it behaves as if that member were replaced with the longest array (with the same
element type) that would not make the structure larger than the object being accessed;
the offset of the array shall remain that of the flexible array member,
even if this would differ from that of the replacement array.
If this array would have no elements,
it behaves as if it had one element but the behavior is undefined
if any attempt is made to access that element or to generate a pointer one past it.

clang++ and g++ accepts this feature in C++ as C99 extensions.
However, as far as I can tell, g++ 6.1.0 doesn't send out any warning
(see <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=71290>)
while clang++ send out a C99-extension warning ([online demo][4]).

# Designated Initialization

Defined at section 6.7.8 of C99.

In this example, line 2 and line 3 have the same meaning

```c
struct point { int x, y; };
struct point p = { .y = yvalue, .x = xvalue };
struct point p = { xvalue, yvalue };
struct { int a[3], b; } w[] =
                { [0].a = {1}, [1].a[0] = 2 };
```

# Static array indices

Defined in #7 of section 6.7.5.3 of C99 (draft)

> A declaration of a parameter as "array of type" shall be adjusted to
"qualified pointer to type", where the type qualifiers (if any) are
those specified within the [ and ] of the array type derivation.
If the keyword static also appears within the [ and ] of
the array type derivation, then for each call to the function,
the value of the corresponding actual argument shall provide access to
the first element of an array with at least as many elements as specified
by the size expression

Example, let's try to compile this code:

```c
void foo(int anArray[static 10]) {
  // We can assume anArray is an array of at least 10 int, if not it's UB
  // From this information, compiler can make some optimization.
}
int main() {
  int a[11];
  int b[7];
  foo(NULL);
  foo(a);
  foo(b);
  return 0;
}
```

Clang complaint:

> z.c:10:3: warning: null passed to a callee that requires a non-null argument [-Wnonnull]
> foo(NULL);
> ^   ~~~~
> z.c:3:14: note: callee declares array parameter as static here
> void foo(int anArray[static 10]) {
> ^      ~~~~~~~~~~~
> z.c:12:3: warning: array argument is too small; contains 7 elements, callee requires at least 10 [-Warray-bounds]
> foo(b);
> ^   ~
> z.c:3:14: note: callee declares array parameter as static here
> void foo(int anArray[static 10]) {
> ^      ~~~~~~~~~~~
> 2 warnings generated.

Unfortunately, gcc doesn't make any complain in this case.

# Restricted Pointer

See [C Restricted Pointer][5]

# Compound Literal

# Complex Numbers

# Generic selection

as describing here: http://en.cppreference.com/w/c/language/generic

# Anonymous structure/union

# K&amp;R function declaration

Honestly, all C Developer who had worked with C from 1990s should know it,
however, most of modern C Developer don't know it..

Good luck to compile this code by C++ compiler:

```c
int foo();

int main(argc, argv)
int argc;
char** argv;
{
  foo(1, 2);
  return 0;
}

int foo(x, y)
int x, y;
{
  return x + y;
}
```

Hence, in C, `void foo()` and `void foo(void)` has difference meaning, however in C++, it's exactly the same.

### To be continued

[1]: http://www.open-std.org/jtc1/sc22/WG14/www/docs/n1256.pdf
[2]: https://gcc.gnu.org/onlinedocs/gcc/Variable-Length.html
[3]: https://busybox.net/~landley/c99-draft.html#6.7.5.2
[4]: http://coliru.stacked-crooked.com/a/90a27407a080bc39
[5]: /programming/2016/08/23/c-restrict-pointer.html
