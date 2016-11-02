---
layout: post
title: C++ Concepts
date: 2016-07-25 18:35:07 +07:00
type: post
published: true
status: publish
categories: programming
tags: [cpp, template]
---

Today, I joined an interview with my company's partner.
In that interview, the interviewer asked me about `concept` in C++.
In that very moment, I was too stressed to be calm enough to answer the question,
I was struggle with it until the third attempt of the interviewer.
Anyway, I had recalled it from my humble memory, I had answered the question.
After the interview, when stepping back to my working desk,
I had a short talk with my colleagues, they asked my about my interview,
I described about my situation and I feel surprised,
NO ONE IN MY GROUP KNOWS ABOUT CONCEPT IN C++.

Hence, I step here, write something about concept in C++.
<!--excerpt-->

In order to understand <code>concept</code>,
you need to understand <code>template</code> first,
if you're not, please take a look at [template][1] first.

While you are using `template`,
you may need to do some computation with the instance of the template parameter,
however, without <code>concept</code>,
C++ compiler would output a weird, long, hard to understand error
if your template parameter doesn't satisfy the requirement of that template parameter.

For example:

```cpp
#include <list>
#include <iostream>
#include <algorithm>

#if defined(__cpp_concepts) && __cpp_concepts >= 201500 
template<typename T>
concept bool HasMemberNamedTestConcept = requires(T t) {
  t.test;
};

template<HasMemberNamedTestConcept T>
auto callConceptAsTemplateParameter(T t) {
  return t.test, t.test;
}
#endif

template<typename T>
#if defined(__cpp_concepts) && __cpp_concepts >= 201500
  requires HasMemberNamedTestConcept<T>
#endif
auto orUseRequireAfterTemplateParamList(T t) {
  return t.test;
}

template<typename T>
auto orUseRequireAfterDeclaration(T t)
#if defined(__cpp_concepts) && __cpp_concepts >= 201500
  requires HasMemberNamedTestConcept<T>
#endif
{
  return t.test;
}

struct NoTest {
};

struct HaveTest {
  int test;
};

int main()
{
#if defined(__cpp_concepts) && __cpp_concepts >= 201500
  std::cout <<  __cpp_concepts;
  auto x1 = callConceptAsTemplateParameter(NoTest{});
  auto y1 = callConceptAsTemplateParameter(HaveTest{});
#endif
  auto x2 = orUseRequireAfterTemplateParamList(NoTest{});
  auto y2 = orUseRequireAfterTemplateParamList(HaveTest{});
  auto x3 = orUseRequireAfterDeclaration(NoTest{});
  auto y3 = orUseRequireAfterDeclaration(HaveTest{});
  return 0;
}
```

When you compile normally in this very moment, we will get these kind of error:

> danh@danh-ws:~/workspace/test$ g++-6 -std=c++1z  concept.cpp
> concept.cpp: In instantiation of ‘auto orUseRequireAfterTemplateParamList(T) [with T = NoTest]’:
> concept.cpp:50:56:   required from here
> concept.cpp:22:12: error: ‘struct NoTest’ has no member named ‘test’; did you mean ‘NoTest’?
> return t.test;
> ~~^~~~
> concept.cpp: In function ‘int main()’:
> concept.cpp:50:56: error: ‘void x2’ has incomplete type
> auto x2 = orUseRequireAfterTemplateParamList(NoTest{});
> ^
> concept.cpp: In instantiation of ‘auto orUseRequireAfterDeclaration(T) [with T = NoTest]’:
> concept.cpp:52:50:   required from here
> concept.cpp:31:12: error: ‘struct NoTest’ has no member named ‘test’; did you mean ‘NoTest’?
> return t.test;
> ~~^~~~
> concept.cpp:52:50: error: ‘void x3’ has incomplete type
> auto x3 = orUseRequireAfterDeclaration(NoTest{});

In case we use a bit more template,
(e.g. when you use some kind of standard library),
we may get likely hundreds lines of meaningless error.

When we use the concept, we will get a nicer error:

> danh@danh-ws:~/workspace/test$ g++-6 -std=c++1z <strong>-fconcepts</strong> concept.cpp
> concept.cpp: In function ‘int main()’:
> concept.cpp:47:52: error: cannot call function ‘auto callConceptAsTemplateParameter(T) [with T = NoTest]’
> auto x1 = callConceptAsTemplateParameter(NoTest{});
> ^
> concept.cpp:12:6: note:   constraints not satisfied
> auto callConceptAsTemplateParameter(T t) {
> ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> concept.cpp:12:6: note:   concept ‘HasMemberNamedTestConcept<NoTest>’ was not satisfied
> concept.cpp:50:56: error: cannot call function ‘auto orUseRequireAfterTemplateParamList(T) [with T = NoTest]’
> auto x2 = orUseRequireAfterTemplateParamList(NoTest{});
> ^
> concept.cpp:21:6: note:   constraints not satisfied
> auto orUseRequireAfterTemplateParamList(T t) {
> ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> concept.cpp:21:6: note:   concept ‘HasMemberNamedTestConcept<NoTest>’ was not satisfied
> concept.cpp:52:50: error: cannot call function ‘auto orUseRequireAfterDeclaration(T) requires predicate( HasMemberNamedTestConcept<T>) [with T = NoTest]’
> auto x3 = orUseRequireAfterDeclaration(NoTest{});
> ^
> concept.cpp:26:6: note:   constraints not satisfied
> auto orUseRequireAfterDeclaration(T t)
> ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
> concept.cpp:26:6: note:   concept ‘HasMemberNamedTestConcept<NoTest>’ was not satisfied

Even if we use a lot of template, the error is still very nice, it's easier to read then.

For more information, please read [constraint][2]

[1]: http://en.cppreference.com/w/cpp/language/templates
[2]: http://en.cppreference.com/w/cpp/language/constraints

