---
layout: post
title: Building Modern C++ with Travis CI
date: 2016-04-28 03:46:00.000000000 +07:00
type: post
published: true
status: publish
categories: programming
tags: [cpp, ci]
---

Recently, I had to work on a test task due to hiring process of a Vietnamese company.

When I tried to build my code automatically when I push new code to GitHub, I had met some trouble with Travis CI, since Ubuntu Travis CI was created from Ubuntu Precise.

After some research, I found these 2 posts:

- <https://jonasw.de/blog/2015/07/22/develop/cplusplus14-on-travis-with-cmake/>
- <http://stackoverflow.com/questions/22111549/travis-ci-with-clang-3-4-and-c11>

However, both 2 posts didn't satisfy my need. Then I combined those 2 post and add a bit more work to create my own `.travis.yml` as in this gist:
<!--excerpt-->
{% gist 2a59f0e4bd0fd66cf61d068f57013add %}

