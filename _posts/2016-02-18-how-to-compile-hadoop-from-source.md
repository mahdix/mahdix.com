---
id: 139
title: How to compile Hadoop from source
date: 2016-02-18T22:24:09+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=139
permalink: /blog/2016/02/18/how-to-compile-hadoop-from-source/
categories:
  - Uncategorized
---
<div dir="ltr">
  Hadoop is a great (and complex) software framework with a lot of dependencies and configurations you need to make. One way to get up to speed with it is to download ready-made release binaries and just install them (You can find related links at <a href="http://hadoop.apache.org/releases.html">http://hadoop.apache.org/releases.html</a>).</p> 
  
  <p>
    But if you want to have the latest source code (and probably work on it), you will need to check them out from Apache Hadoop's git repository. Before you can run Hadoop this way, you will need to set-up your system. This setup will include:<br />1. Installing required OS packages (autoconf, cmake, libtool, &#8230;)<br />2. Installing JDK and Maven<br />3. Installing protocol buffers with the correct version<br />4. Installing Hadoop maven plugins<br />5. Compiling and installing Hadoop<br />6. Setting up password-less SSH and some environment variables
  </p>
  
  <p>
    I have prepared a 'Vagrantfile' with instructions to provision the vagrant machine to do above steps. All required steps are explained in README file of the repository: <a href="https://github.com/mm-binary/hadoop-src-getting-started">https://github.com/mm-binary/hadoop-src-getting-started</a></div> 
    
    <p>
    </p>