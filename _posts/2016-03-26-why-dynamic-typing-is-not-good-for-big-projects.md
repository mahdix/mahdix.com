---
id: 164
title: Why dynamic typing is not good for big projects
date: 2016-03-26T00:04:50+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=164
permalink: /blog/2016/03/26/why-dynamic-typing-is-not-good-for-big-projects/
categories:
  - Uncategorized
---
The other day I was updating an algorithm for searching some data elements. The general structure of the code was something like this (btw we use Perl):

<pre class="brush: perl; title: ; notranslate" title="">my $from = Date::Utility-&gt;new($finput)-&gt;epoch;

my $to = Date::Utility-&gt;new($tinput)-&gt;epoch;

#...

my $result = $chronicle_reader-&gt;get_for_period("category1", "title1", $from-&gt;minus_time_interval("1d"), $to-&gt;plus_time_interval("1d"));

return $result;

</pre>

Did you notice the problem with above code?

_Epoch_ is an integer number. Both _$from_ and _$to_ variables store epochs, so they have integers. I was assuming their type to be _Date::Utility_ which has some utility methods (E.g. _plus\_time\_interval_). Problem is, the above code does not cause any error or warning by Perl interpreter until that specific line is executed. Of course this could be prevented by writing a unit test which covered that line, but hey! why write a unit test just to do what can be done automatically by software?

Had we used a static-typed programming language like Java or C#, this would be detected upon compiling the code. Of course using Perl has it&#8217;s own advantages but detecting such issues is not one of them.