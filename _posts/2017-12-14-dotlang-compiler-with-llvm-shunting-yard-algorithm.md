---
id: 389
title: 'dotLang compiler with LLVM &#8211; Shunting-yard algorithm'
date: 2017-12-14T21:12:39+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=389
permalink: /blog/2017/12/14/dotlang-compiler-with-llvm-shunting-yard-algorithm/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"2cad730892a6";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:90:"https://medium.com/@mahdix/dotlang-compiler-with-llvm-shunting-yard-algorithm-2cad730892a6";}'
categories:
  - Uncategorized
---
This is the third post in a series of posts explaining about my adventure in writing a compiler for my own language: dotLang. You can see previous posts [here](http://www.mahdix.com/blog/).

Initially, I was relying heavily on EBNF notation to explain the notation for the language. This proved to be very useful in all areas except for expressions (which is an important part of the language). The reason is that, we need to pay attention to precedence and associativity in an expression (how are we going to parse `1+2*3`?) So, a normal EBNF rule to explain all different operators that can be combined would not be helpful. You will need to write operators in different levels to take into account their precedence.

This is the EBNF notation for an expression (excluding some advanced operators which are not the focus of the project at the moment):

    Expression         = EqExpression     { ("and"|"or"|"xor") EqExpression }
    EqExpression       = CmpExpression    { ("="|"!=") CmpExpression }
    CmpExpression      = ShiftExpression  { (">"|"<"|">="|"<=") ShiftExpression }
    ShiftExpression    = AddExpression    { (">>"|"<<"|"^") AddExpression }
    AddExpression      = MulExpression    { ("+"|"-") MulExpression }
    MulExpression      = UnaryExpression  { ("*"|"/"|"%"|"%%") UnaryExpression }
    UnaryExpression    = ["not"|"-"]      PrimaryExpression
    PrimaryExpression  = ( BINDING_NAME | "(" Expression ")" | ExpressionLiteral )
                         {  "(" Expression* ")" | "." Expression | "[" Expression "]" }
                         (*  function call      / struct access    / seq/map access    *)

As you can see I have to define seven different rules so that I can have a higher precedence for `not` compared to `>`. And this is continued in the code of the compiler, making it full of different structs (Yes, I&#8217;m using C) nested into each other. Although the recursive nature of this structure makes it easy to traverse into the data, but fixing a bug or adding something new is very difficult.

Because of that, I have now switched to another algorithm and another notation. I use [Reverse Polish Notation](https://en.wikipedia.org/wiki/Reverse_Polish_notation) (RPN) to represent an expression (which is essentially a linked list of elements) and [Shunting-yard algorithm](https://en.wikipedia.org/wiki/Shunting-yard_algorithm) to translate a textual representation of an expression into RPN notation. So for example `a+b*c` will become `a b c * +`. The advantage of this representation is that it&#8217;s much more easier to parse and generate code because we don&#8217;t need to keep a state of the current status. Just push operands, and pop them when you see an operator and push the result again.

This is the data structure that I have for an expression:

<pre>typedef struct Expression
{
 ExpressionNode *first_node;
} Expression;

typedef struct ExpressionNode
{
 char token[32];
 TokenKind kind;
 struct ExpressionNode* next;
} ExpressionNode;</pre>

Basically, an expression is just a singly linked list. Each node has a token and it&#8217;s kind. So the token can be `a` and kind can be `IDENTIFIER`.

I have tried to make the parser as dumb as possible. So the code would be as simple as possible. This allows me to enhance/optimize/refactor the code in future more easily.

[Here](https://github.com/dotlang/dot/blob/v0.3.0/src/parsers.c#L22) you can see the code that parses an expression from the input file. and [here](https://github.com/dotlang/dot/blob/v0.3.0/src/expression_compiler.c#L6) is the code that given the linked list representing an expression, invokes appropriate LLVM API to generate executable code.

One of the complexities involved in parsing the expression is a function call. Naturally, function calls are in &#8220;prefix&#8221; format where name of the function comes before operands. On the other hand, normal math expressions are infix. And when I want to convert all of these to postfix notation, I have to use different approaches to handle math expressions and function calls.

As a result, I chose to treat comma `,` as a special operator which means &#8220;join previous two operands as function arguments&#8221;. And have two types of function calls: simple function call (where there is no argument) and normal function call (which has at least one argument).

So `process(x,y,z)` becomes `x y , z , process`. So when processing the postfix notation, after seeing a comma, we know that previous two operands were function arguments.

In the next post, I will explain how function declaration is done in LLVM.

&nbsp;

&nbsp;