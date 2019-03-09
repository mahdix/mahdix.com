---
id: 135
title: Git grep tips
date: 2016-02-18T11:15:36+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=135
permalink: /blog/2016/02/18/git-grep-tips/
categories:
  - Uncategorized
---
If you are working with terminal-based editors like vim, you will need  
to search for a text pattern a lot of times. This can be required when  
you want to re-factory a symbol and want to find all references to  
that symbol in the code.

In order to search for a text in a git repository you can use

<pre class="brush: plain; title: ; notranslate" title="">git grep</pre>

command. This command will search for given text  
across all source files in the current repository.

Examples:

<pre class="brush: plain; title: ; notranslate" title="">git grep Keyword   #will find all lines of code which mention `Keyword`
git grep -A 3 ABCD #same as above but will include 3 lines after each match
git grep -B 3 ABCD  #display 3 lines before each match
git grep -C 3 ABCD   #display 3 lines before and 3 lines after each match
git grep --line-number ABCD #show line-number of each match too
git grep --break ABCD  #put a line break between matches for each file
</pre>

I normally define this line in my ~/.bashrc file:

<pre class="brush: plain; title: ; notranslate" title="">git config --global alias.fn    "grep --break --heading --line-number"
</pre>

So you can simply write

<pre class="brush: plain; title: ; notranslate" title="">git fn TEXT </pre>

and it will show a  
tidy output separated file by file and will group matching in each  
source file.