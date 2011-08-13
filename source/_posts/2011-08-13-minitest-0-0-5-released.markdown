---
layout: post
title: "minitest-0.0.5-released"
date: 2011-08-13 14:46
comments: true
categories: devops opschef lwrp minitest
---

A few days ago I released version 0.0.5 of the [MiniTest
Cookbook](https://github.com/fujin/minitest-cookbook) which included a
feature I've been working on that allows you to build MiniTest unit
test cases programmaticaly throughout the Chef run and have them
executed by a dedicated test runner.

``` ruby
    minitest_unit_testcase "test something" do
      block do
        refute_equal true, false, "true is not false"
        assert_equal true, true, "true is not equal true
      end
    end
```

I've also retained the ability to run via Vagrant, for ease of
testing. The [develop branch](https://github.com/fujin/minitest-cookbook/tree/develop) has some more recent enhancements, notably
an in-line Sinatra server which I'm building example test cases
around.

Test all the fucking time.
