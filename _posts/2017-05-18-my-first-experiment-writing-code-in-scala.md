---
id: 302
title: My first experiment writing code in Scala
date: 2017-05-18T12:35:49+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=302
permalink: /blog/2017/05/18/my-first-experiment-writing-code-in-scala/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"d539c94d11c5";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:81:"https://medium.com/@mahdix/my-first-experiment-writing-code-in-scala-d539c94d11c5";}'
categories:
  - Uncategorized
---
_(This post was written on Nov 17, 2016. For some reason I forgot to publish it at that time)._

For the past few days, I have been assigned a small programming task which should be done in [Scala](https://www.scala-lang.org/). This is part of a hiring process and I have been asked to implement the server side of a tic-tac-toe game in Scala providing a REST API.

For the past few days, I have been assigned a small programming task which should be done in Scala. Now, this is the first time I am writing code in Scala language ever. So although I am not a professional Scala developer yet, I thought I should write down about my experience here.

## The Good

I really like the language. It is simple and straight-forward. The [principle of least astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment) was obeyed and I had very little surprises when I wanted to see how something is done, which is really good.

One of the positive points was the fact that there is no separation between primitive and Object-based data types. So we have &#8220;`Int`&#8221; and not &#8220;`int`&#8221; + &#8220;`Integer`&#8221; (Like Java). This helps a lot.

I also liked the way objects are defined: Like a function which accepts a set of inputs:

`class Game(`val `player1: String,` val `player2: String) { ... }`

Also the \``var`\` and \`val\` separation is really encouraging the developer to make the best decision about the immutability of the data he is working with. It makes you think in a new way.

In Scala, you can use Java libraries easily without any pain. That gives you access to thousands of valuable frameworks and modules.

## The Bad

Well, this section is mostly personal preference.

In Scala (unlike C and Java and similar languages), you should obey &#8220;`variable` name : `type`&#8221; rule which is not normal for me, considering the fact that I have worked for many years in C-like languages which have: &#8220;`type variable_name`&#8221; notation.

The &#8220;`=`&#8221; notation when defining a function which is returning a value, does not seem natural to me. Usually, you use &#8220;`=`&#8221; when you want to assign a value to a variable but the functions are not variables!

Same for the &#8220;`<-`&#8221; notation which is used to iterate over a collection.

Also I noticed there is no &#8220;`break/continue`&#8221; keywords in Scala. Well, it is possible to &#8220;`simulate`&#8221; them using a set of built-in keywords and exceptions, but seriously! exceptions are not invented to break out of a for loop! You should not use them to control program execution.