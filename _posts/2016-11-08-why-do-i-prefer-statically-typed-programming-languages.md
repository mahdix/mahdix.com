---
id: 296
title: Why do I prefer statically typed programming languages?
date: 2016-11-08T06:30:42+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=296
permalink: /blog/2016/11/08/why-do-i-prefer-statically-typed-programming-languages/
categories:
  - Uncategorized
---
**tldr;**  Dynamic typing sucks!

I have been working with a dynamic programming language, Perl, for the past 1.5 years. I also have worked with Java and C# languages during my career and at this point, I can really say that static typing makes the life of developers much easier.

So let me first explain the difference between these two terms and then I will talk about my reasons.

## Dynamic Typing

Dynamically typed programming languages, are languages where the type of the data is not specified in the source code, but when the program is being executed. So the developer is not responsible for thinking about whether some variable should hold a number, string or an instance of `DateTime` object. He just uses a variable name. When the code is being executed, the runtime environment (interpreter, JIT compiler, &#8230;) will deduce the type which is most appropriate for that variable.

For example, the developer can write something like this, in Perl:

<pre>my $birth_date = Some::Module::get_birth_date();
$birth_date-&gt;plus_time_interval('18y');
my $day_of_week = $birth_date-&gt;day_of_week;</pre>

In the above code, type of &#8216;`$birth_date`&#8216; and &#8216;`$day_of_week`&#8216; are not explicitly specified. When someone reads this piece of code, it clearly implies that \``$day_of_week`\` should be an integer number, but in a large codebase, this is not always possible when you look at the code. So, for example, you really don&#8217;t know the type of \``$birth_date`\` unless you read the source code of the \``get_birth_date`\` function, or you can&#8217;t know if \``plus_time_interval`&#8216; expects an integer or string or something else unless you have used that function before.

Now assume someone is responsible for maintaining or fixing a bug in a 100K SLOC codebase. When investigating the flow of execution of the code, it sometimes becomes really hard and complicated to find out the real type of a variable.

Examples of dynamically typed programming languages are: Perl, Python and Ruby and Javascript.

## Static typing

On the other hand, in statically typed programming languages, the developer is responsible to explicitly state the expected type of all of the variables or function outputs. This will limit the scope of valid operations on a variable and help the compiler (or possibly the interpreter) to check for invalid operations. For example, when the developer defines an integer variable, he cannot treat that variable as a string or assign a string literal to it because the compiler will catch such errors. An example of statically typed code:

    int x = 12;
    int y = x+y;
    Customer c = find_customer("id", y);
    c.name = "Mahdi";
    c.save();

In the above code, type of the variables \``x`\`, \``y`\` and the input/output of the function \``find_customer`\` are all specified statically within the source code. You cannot pass an integer parameter as the first argument of \``find_customer`\` and compile the code because you will get a compiler error.

So writing a statically typed code seems harder because the developer should write more code and keep track of variable types in his mind when writing code. Right? Yes! That is right if and only if you want to write a few lines of code and keep that for a relatively short period of time and ignore all of that afterward. But if you want to keep a relatively large code base over a longer period of time (which is the case most of the time, in thr software industry) this burden proves to have some benefits.

But if you want to keep a relatively large code base over a longer period of time (which is the case most of the time, in the software industry) this burden proves to have some benefits.

Examples of statically typed programming languages include C, C++, Java and C#.

## Why static typing?

As I pointed out in the previous section, unless you want to write a few lines of code, keep it for a short period of time and discard everything afterward, the statically typed programming language have a clear advantage over dynamically typed ones.

The point is, the source code of a software is written once, but it will be read a lot of times (for review, bug fix, refactoring, migrations, optimizations, &#8230;). So the basic idea in static typing is that you write some more code and take care of some more things in the code when writing the code, and in exchange, you (or others) will have a much easier life when they want to read it. Another clear benefit is that compiler can help you find out lots of possible errors before releasing your code to the production environment.

I have had numerous cases of &#8220;`Can't locate object method 'xyz' via package "ABC::DEF"`\` error in production server (Because in the code, someone was trying to call a method on a variable which was expected to be of type T, but at runtime, it had another type). That is because of we, human beings, make mistakes. And computers are there to help us prevent those mistakes. If we just ignore their help (And use dynamic typing), then this is what happens next 🙂

But don&#8217;t get me wrong. There are cases where dynamic typing is the preferred choice when writing a software system. Generally speaking, if the code base size is relatively small (say 1-10K SLOC) with a small development team (~5 people), then dynamic typing can have benefits. Because obviously developers can write the code much faster and time-to-market will be much shorter. And this is what normally happens for startups where founders want to deliver the product/prototype as soon as possible.

I have seen some people say that we can achieve same advantages of static typing in a dynamically typed programming language by writing good tests. This is possible in theory but if you implement all of the required test cases for this purpose, then you will be doing what the computer is supposed to do! Manually doing something which can be automated. And by the way, the result code based will become much larger because of all those tests! which eliminates one of the important advantages of dynamic typing: more concise code with less boilerplate.