---
id: 272
title: What is wrong with Object Oriented Programming and what is right with Functional Programming?
date: 2016-10-10T00:58:02+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=272
permalink: /blog/2016/10/10/what-is-wrong-with-object-oriented-programming-and-what-is-right-with-functional-programming/
categories:
  - Software Design
---
I have been reading numerous articles about OOP and FP and comparison of these two recently. There are lots of differences and pro/cons for each of them but one important and interesting thing I recently found out was the fact that how tangled behavior and data are in OOP. I have been writing OO code for years, but I had not found out this until I read about FP and the way it handles data/behavior.

When you write a software in an OO approach, you must relate every behavior to a single isolated data entity (or class) in the business domain of the application. For example, when writing a code to send email, you have a data entity called &#8220;Email Message&#8221; and a behavior &#8220;Send a message&#8221;. There is no way for you to define these two separately. By separately I mean being able to invoke &#8220;Send Email Message&#8221; functionality without an &#8220;Email Message&#8221; object. Of course, you need a message when you want to send one, but this is a difference in the way we organize and approach things in OOP. When writing software in OO approach, you MUST define each behavior under one and exactly one class.

Question is, what if we cannot fit a behavior in this pattern? What if it doesn&#8217;t make sense or it is confusing to attach a behavior to one and only one class?

Considering above example about sending an email message, what happens if we have other entities in the application like Sender, Recipient, and Storage. Then where should I put &#8220;Send email message&#8221; functionality? Each one of the below candidates can be an acceptable place for putting this behavior.

  1. You can put &#8220;Send&#8221; method in &#8220;Message&#8221; class: `msg1.Send();`
  2. It can also be placed inside Recipient class: `recipient.ReceiveMessage(msg1);`
  3. It can also be part of Sender class: `sender1.SendMessage(msg1);`
  4. It can be part of a separate class. For example MessageBroker: `MessageBroker.getInstace().SendMessage(msg1);`

This is really confusing and IMHO unneeded complexity.

The advantage of Functional Programming approach is that you don&#8217;t have to bind each behavior to one class. They are completely separated. So you can define and use them separately. This type of modeling is more consistent with the way we think about software world concepts.

Of course for things which exist in the physical world, this is less of an issue (A car is the only object which can do the &#8220;Start&#8221; functionality). But for more abstract concepts which can only be found in the software world, I think FP approach makes more sense.

&nbsp;