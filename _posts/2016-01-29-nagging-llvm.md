---
id: 90
title: 'Nagging: LLVM'
date: 2016-01-29T00:49:17+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=90
permalink: /blog/2016/01/29/nagging-llvm/
categories:
  - Uncategorized
---
These days I am getting familiar with LLVM and its IR language. As a part of this process I try to run some sample codes to see how it works. But guess what? NONE of the samples are working with the current LLVM installation that I have.

I tried <https://github.com/paulsmith/getting-started-llvm-c-api> first. It does no compile because \`jit\` is no longer part of LLVM and \`<span style="color: #0086b3; font-size: 12px; line-height: 16.8px; white-space: pre;">LLVMLinkInJIT` </span>is referenced in the source code!

<div>
</div>

<div>
  Same issue with <a href="https://github.com/benbjohnson/llvm-c-kaleidoscope">https://github.com/benbjohnson/llvm-c-kaleidoscope</a>.
</div>

<div>
</div>

<div>
  I mean, after 13 years of development, its time to stabilize something and let other USE your product. Isn&#8217;t it?
</div>

&nbsp;

&nbsp;