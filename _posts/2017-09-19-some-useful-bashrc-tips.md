---
id: 330
title: Some useful .bashrc tips
date: 2017-09-19T05:42:30+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=330
permalink: /blog/2017/09/19/some-useful-bashrc-tips/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"f491831492ad";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:63:"https://medium.com/@mahdix/some-useful-bashrc-tips-f491831492ad";}'
categories:
  - Uncategorized
---
When cleaning up my .bashrc file, I noticed some useful shortcuts that might be useful for others. Here they are:

### SSH with .bashrc

Sometimes I need to SSH to a new machine and will need my `.bashrc` file there. This short function, transfers my local `.bashrc` file to the remote server and then does the SSH:

<pre class="brush: bash; title: ; notranslate" title="">function xs() {
if [ "$#" -eq 0 ]; then
    echo "usage: xs user@host other_args"
    return
fi

host=$1

shift

echo "Setting up remote machine..."
scp $@ ~/.bashrc $host:/tmp/.bashrc_temp &gt; /dev/null

echo "Connecting to $host"
ssh -A -t $@ $host "bash --rcfile /tmp/.bashrc_temp ; rm /tmp/.bashrc_temp"
}

</pre>

### Perl

Alias to invoke Perl debugger and a REPL for running perl code snippets:

<pre class="brush: perl; title: ; notranslate" title="">alias perld='PERLDB_OPTS="windowSize=20 arrayDepth=300 hashDepth=300 dumpDepth=100" perl -MPerlIO='"'"'via;$DB::deep=9999'"'"' -d $@'
alias repl='PERLDB_OPTS="windowSize=20 arrayDepth=300 hashDepth=300 dumpDepth=5" perl \
-MPerlIO='"'"'via;$DB::deep=9999; \
'"'"' -de0'

</pre>

### Highlighter

This simple function can be piped after an output command and hight a specific keyword. Useful when you are looking for a specific text but want to see the whole context in addition to the keyword:

<pre class="brush: bash; title: ; notranslate" title="">#highlight the given text in the input (cat log.txt | highlight ERROR)
highlight () {
perl -pe "s/$1/\e[1;31;21m$&\e[0m/g"
}

</pre>

### vim

Open the last output using vim:

You can use `fc -s` command to re-execute last command and feed it&#8217;s output to `vim` to open it. This might be useful in cases where you are looking for a file and then want to open it

<pre class="brush: bash; title: ; notranslate" title="">alias viml='vim $(fc -s)'</pre>

### Single letter aliases

Due to frequent usage, I have these 3 single-letter aliases:

<pre class="brush: bash; title: ; notranslate" title="">alias g='git'

alias k='kubectl'

alias d='docker'

</pre>