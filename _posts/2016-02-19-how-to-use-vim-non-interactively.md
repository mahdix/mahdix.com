---
id: 140
title: How to use vim non-interactively
date: 2016-02-19T23:31:37+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=140
permalink: /blog/2016/02/19/how-to-use-vim-non-interactively/
categories:
  - Uncategorized
---
<div dir="ltr">
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
    If you want to use some feature of vim non-interactively, you can use `-c` command argument. This will accept a vim command and will execute it on the input file.
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
    You can use multiple -c arguments. For example:
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
    <pre class="brush: plain; title: ; notranslate" title="">vim -c '%s/data/DATA/g' -c 'wq' myfile</pre>
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
    This will replace all occurrences of `data` with `DATA` in `myfile` automatically.
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
    Tip: You can combine this with `find` command and have an automated find-and-replace script which acts on a set of files.
  </div>
  
  <div class="gmail_default" style="font-family: tahoma,sans-serif;">
  </div>
  
  <p>
    &nbsp;
  </p>
</div>

&nbsp;