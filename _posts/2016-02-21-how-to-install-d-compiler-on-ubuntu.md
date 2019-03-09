---
id: 142
title: How to install D compiler on Ubuntu
date: 2016-02-21T01:33:31+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=142
permalink: /blog/2016/02/21/how-to-install-d-compiler-on-ubuntu/
categories:
  - Uncategorized
---
The problem is, on Ubuntu you cannot simply run 

<pre class="brush: plain; title: ; notranslate" title="">apt-get install dmd</pre>

to install D compiler. I hope they add it to official repositories someday but till then you can try this:

First you will need to download appropriate .deb file from D official website: [link](https://dlang.org/download.html#dmd)

Then:

<pre class="brush: plain; title: ; notranslate" title="">apt-get install gdebi-core

sudo gdebi dmd_2.070.0-0_amd64.deb

</pre>

Then you can compile any .d source file using 

<pre class="brush: plain; title: ; notranslate" title="">dmd file1.d</pre>