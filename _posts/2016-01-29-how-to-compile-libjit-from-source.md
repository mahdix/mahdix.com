---
id: 123
title: How to compile libjit from source
date: 2016-01-29T23:21:39+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=123
permalink: /blog/2016/01/29/how-to-compile-libjit-from-source/
categories:
  - Uncategorized
---
[Libjit](https://www.gnu.org/software/libjit/) is a GNU library which can be used to generate machine code on the fly. You can use this library to add Just-in-Time compilation to your interpreter.

This is the commands that is needed to get latest source and compile this library.

<pre class="brush: bash; title: ; notranslate" title="">sudo apt-get install git

sudo apt-get install flex

sudo apt-get install bison

sudo apt-get install make

sudo apt-get install autotools-dev

sudo apt-get install autoconf

sudo apt-get install libtool

sudo apt-get install gcc

sudo apt-get install texinfo

git clone git://git.savannah.gnu.org/libjit.git

cd libjit

./auto_gen.sh

./configure

make

make install

</pre>

The whole process is tested on a bare Ubuntu 15.10 x64 and takes ~5 minutes.