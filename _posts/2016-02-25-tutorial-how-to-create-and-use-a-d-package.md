---
id: 147
title: 'Tutorial: How to create and use a D package'
date: 2016-02-25T21:29:44+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=147
permalink: /blog/2016/02/25/tutorial-how-to-create-and-use-a-d-package/
categories:
  - Uncategorized
---
To create a D package, you first need to have **dub** utility (plus D compiler, dmd). You can download pre-compiled dub binaries from [here](https://code.dlang.org/download).

### Creating a D package

<pre class="brush: bash; title: ; notranslate" title="">cd ~

mkdir dlibs

cd dlibs

dub init mypack  #this will create a skeleton for your package: mypack

cd mypack

cat dub.sdl  #below is contents of your package

name "mypack"
description "A minimal D application."
copyright "Copyright © 2016, mahdix"
authors "root"
version "1.0.0"
targetType "library"


#the package will have only one source file, located inside source directory

cat source/myfile.d

int add(int x,int y) {
return x+y;
}

#this will compile your package

#although its not necessary but ensures that everything is allright.

dub

Performing "debug" build using dmd for x86_64.
mypack ~master: target for configuration "library" is up to date.
Target is a library. Skipping execution.

</pre>

### Using a D package

To use a D package, we will create an application (which is itself a D package) and define the package we need as it&#8217;s dependency. But before that, we need to inform **dub **where the packages are (which is inside ~/dlibs).

<pre class="brush: bash; title: ; notranslate" title="">dub add-path ~/dlibs

dub list #this will list all known packages to dub, which will include mypack too

</pre>

Now lets create a basic project that uses the package:

<pre class="brush: bash; title: ; notranslate" title="">cd ~

dub init project1

cd project1

cat dub.json #config file can be either sdl, they are equivalent

{
"name": "myproject",
"description": "A little web service of mine.",
"authors": ["Mahdi Mohammadi"],
"homepage": "http://myproject.example.com",
"license": "GPL-2.0",
"targetType": "executable",
"dependencies": {
"mypack": "*"
}
}

#as you notice, we specify "*" as version number of mypack which means: any version

cat source/test.d #this is our only source file which uses add method of mypack

import std.stdio;
import myfile;
void main() {
int x = add(1,4);
writefln("%d", x);
}

#now lets build project1

pwd

~/project1

dub

Performing "debug" build using dmd for x86_64.
mypack ~master: target for configuration "library" is up to date.
myproject ~master: target for configuration "application" is up to date.
To force a rebuild of up-to-date targets, run again with --force.
Running ./myproject
5

#5 is output of the call to add function in mypack

</pre>

&nbsp;

&nbsp;