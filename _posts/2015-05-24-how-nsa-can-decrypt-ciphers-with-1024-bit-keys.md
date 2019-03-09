---
id: 41
title: How NSA can decrypt ciphers with 1024 bit keys?
date: 2015-05-24T07:02:23+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=41
permalink: /blog/2015/05/24/how-nsa-can-decrypt-ciphers-with-1024-bit-keys/
categories:
  - Uncategorized
---
<div dir="ltr">
  <div class="gmail_default" style="font-family:tahoma,sans-serif">
    <a href="http://www.scottaaronson.com/blog/?p=2293">Here</a> is the complete story.
  </div>
  
  <div class="gmail_default" style="font-family:tahoma,sans-serif">
    The story is: Most of today's secure network data transfers (e.g. when you enter your credit card number in a website), is done based on protocols which are using Diffie-Hellman algorithm. This algorithm's implementation uses some fixed numbers as its input parameters.
  </div>
  
  <div class="gmail_default" style="font-family:tahoma,sans-serif">
    NSA (supposedly) has pre-calculated different computation results for the single most common used parameter and using this set of pre-calculated data (plus several hundred CPU cores), it can decrypt text which is encrypted using 1024 bit keys.
  </div>
  
  <div class="gmail_default" style="font-family:tahoma,sans-serif">
    If you use 2048 bit keys they will not be able to do this but currently most of client and server applications are pre-configured to use this type of key. 
  </div>
  
  <p>
    </div> 
    
    <p>
    </p>