---
id: 387
title: dotLang – Writing a Compiler using LLVM – Part 2
date: 2017-12-05T08:17:23+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=387
permalink: /blog/2017/12/05/dotlang-writing-a-compiler-using-llvm-part-2/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"53c090f1339b";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:84:"https://medium.com/@mahdix/dotlang-writing-a-compiler-using-llvm-part-2-53c090f1339b";}'
categories:
  - Uncategorized
---
This is the second post in a series which explains how I am writing a compiler for [dotLang](http://dotlang.org) in C using LLVM.

So the basic compiler is supposed to compile a simple function (called `main`) with simple expressions and possibly bindings.

Generally, a compiler can be considered like a data pipeline. At the beginning of the pipeline we have the source code file written in dotLang, and at the end of the pipeline, we have a compiled executable.

The important steps that happen in that pipeline are:

  1. Lexer: We need a module to read from input file and give us tokens. Tokens are smallest units of the source code (identifiers, operators, numbers, &#8230;).
  2. Parser: This component receives data from lexer and creates a syntax tree. A syntax tree is an abstract representation of the source code which is simpler to process and generate native code.
  3. Code generator: This part, receives a syntax tree and parses the tree, node by node. For each node, it makes calls to LLVM library to generate appropriate native code.

As the simplest example, suppose we have this source code:

&nbsp;

main := () -> 1+2

Now the first part (Lexer), will give us these tokens: `main, :=, (), ->, 1, +, 2`

The second part will create this syntax tree:

<pre>main (Binding) ------&gt; Operator: Plus |-------&gt; 1
                                      |-------&gt; 2</pre>

The third part will make these calls (ignoring the boilerplate we need to do to define and setup an LLVM module):

<pre>LLVMValueRef a = LLVMConstInt(int_type, 1, true);
LLVMValueRef b = LLVMConstInt(int_type, 2, true);
LLVMBuildAdd(builder, a, b, "add");</pre>

So at the end, the problem can be decomposed into writing a lexer, parser, and a code generator.

Of course, the devil is in the details. So there are some issues that you have to think about:

  1. What are your operators and their precedence?
  2. Where/how are you going to allocate memory for local variables?
  3. How are you going to manage and reclaim their memory?
  4. There might be some ambiguous cases where you really don&#8217;t know what does a specific token mean.

So you will need some kind of grammar for your language. This will help you with items #1 and 4 on the above issues. For #2 and #3, you will need to make some decisions and evaluate different pros/cons for each option that you have.

For dotLang, I decided to go with Reference-Counting garbage collector and use stack memory for primitive local variables (int, char, float, and bool).

This is another example of a more complex source code that dotLang can compile right now:

<pre>main := () -&gt; int
{
  :: betha()/11
}

betha := () -&gt; int
{
  #this will return 33
  ddd := 3
  :: (2+gamma()+20)+alpha()+ddd-3
}

alpha := () -&gt; int
{
  #this will return 1
  :: 11-gamma()
}

gamma := () -&gt; int
{
  #this will return 10
  :: 1+2+3+(1*4)
}</pre>

You can see the full source code in `v0.2.0` release of the project.

One of the interesting points when parsing source code similar to above example, is that by default, you cannot call a function before defining it. But as you can see I call `gamma` inside `alpha` or `betha` inside `main`. So how does that work?

The answer is using a two-phase code generation. In the first phase, I just add functions (their name, input and output type), and in the second step, I add instructions to their body. This way, when I want to compile a call to `gamma` inside `alpha` I have some reference for that function which I can use to generate the call.

To keep track of local variables, I use a hashtable. One good thing about LLVM is that, almost everything is stored as `LLVMValueRef`. Functions, values, operation results, &#8230; are all accessible as instances of this type. So when you add two integers, each of them is LLVMValueRef and the result of add is also of the same type. So you can easily mix different operations together.

In the source code, `basic_parsers.c` implements basic Lex operations. I named them as parsers because Lex and Parse steps are two connected and mixed steps. The actual parsers are in `parsers.c` file. Code generation is in `compilers.c` and `expression_compiler.h`.