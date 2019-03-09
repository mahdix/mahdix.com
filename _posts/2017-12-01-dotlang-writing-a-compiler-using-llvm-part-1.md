---
id: 380
title: 'dotLang &#8211; Writing a Compiler using LLVM &#8211; Part 1'
date: 2017-12-01T13:44:32+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=380
permalink: /blog/2017/12/01/dotlang-writing-a-compiler-using-llvm-part-1/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"d0e630184f64";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:84:"https://medium.com/@mahdix/dotlang-writing-a-compiler-using-llvm-part-1-d0e630184f64";}'
categories:
  - Uncategorized
---
So, I have been working on &#8220;dotLang&#8221; for more than 2 years now. It has been a great journey up to now. I have been working mostly on the language spec (memory model, type system, function call dispatch, operators, &#8230;). But it&#8217;s time to move on to the next step which is writing the compiler. I will try to update this series regularly with the latest status on writing the compiler.

I chose to write the compiler in C because of its simplicity. That is my main goal in creating dotLang so it made sense for me. Also I will be using LLVM to generate native code because I am not going to invent the wheel again!

The first step is writing the grammar of the language. Now this grammar will definitely get updated during the compiler implementation project but right now it can be viewed [here](https://github.com/dotlang/language/blob/master/syntax.md).

I have used a modified EBNF notation to describe the grammer. First I was thinking that a grammar is not really an important part but soon I found out that it will act like a map in an uncharted territory. It helps you maintain the big picture while working on the smallest details. Another important advantage is that it makes you think really carefully about the notations and semantics in your language and this helps you find out if there are any inconsistencies or ambiguities in the rules of your language.

So the basic building block of the language is a `Module`. A module can contain three different elements:

  1. Named type definitions: Assigning a name for a specific type (For example &#8220;`MyInt := int`&#8221; ).
  2. Imports: Including definitions from another module (&#8220;`_ := @{"module"}`&#8220;, here underscore means we want to filter the output of the command and want to import everything).
  3. Bindings: Defining a function or a value to be used/invoked later (Bindings are immutable data definitions, e.g. &#8220;`PI := 3.1415`&#8221; or &#8220;`inc := (x:int) -> x+1`&#8220;).

A binding in dotLang can be any immutable value. Note that even functions are considered values in dotLang.

I will start with manually writing a lexer and parser. The parser will be a Recursive Descent parser. I tried to use Flex/Bison before but did not really like the huge volume of code they generated. Also, it was difficult to track errors in the code being compiled.

About LLVM one thing that really shocked me was how scarce the documentation/reference material is for C bindings. So all I got was something like this:

[<img class="alignnone  wp-image-383" src="http://www.mahdix.com/wp-content/uploads/2017/12/llvm-doc.png" alt="" width="536" height="190" />](http://www.mahdix.com/wp-content/uploads/2017/12/llvm-doc.png)

Well, does not say a lot of things about how the function works, what are its inputs and outputs. There is also a ton of blog posts for writing a simple tiny compiler using LLVM C bindings but they don&#8217;t compiler with the current version of LLVM. I guess they are so busy adding new functionalities or removing/deprecating stuff in LLVM that they do not have enough time to document their code.

However, I managed to find some sort of documentation/manual which proves to be very useful when implementing the compiler.

Here is the list:

  * [LLVM Programmer&#8217;s Manual](http://llvm.org/docs/ProgrammersManual.html#id79)
  * [LLVM Language Reference Manual](https://llvm.org/docs/LangRef.html)
  * [Summus: Simple compiler frontend using LLVM as backend](https://github.com/igor84/summus)

In the next post, I will start to explain the process of writing the compiler.