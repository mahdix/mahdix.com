---
id: 356
title: 'Code snippet: How to manually validate a Bitcoin block?'
date: 2017-07-08T11:36:14+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=356
permalink: /blog/2017/07/08/code-snippet-how-to-manually-validate-a-bitcoin-block/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"ce746716adb0";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:93:"https://medium.com/@mahdix/code-snippet-how-to-manually-validate-a-bitcoin-block-ce746716adb0";}'
categories:
  - Uncategorized
---
The other day, I was trying to understand how Bitcoin works. There are a lot of references that tell you some general description of the cryptocurrency, but a few of them get to the real details about the internals.

The blog post on [1] was really helpful, although it did not act as I wanted. After a little searching here and there, finally, I managed to prepare a Python code snippet you can use to manually make sure a Bitcoin block is valid.

This code represents the underlying design of a Bitcoin mining algorithm. Miners try with different arguments (most importantly, nonce) to come up with a hash result which starts with enough zeros.

Here is how you can use this code:

You need to provide six inputs to the code:

  1. **version: **The version number (Normally 2 but can differ in some cases)
  2. **prev_block**: The hash of the previous block
  3. **merkle_root: **A hash of the Merkle root
  4. **date_time: **Time in which the block is mined
  5. **bits: **A parameter related to difficulty level
  6. **nonce****: **A random number generated as a part of mining the block

You can find these numbers easily in [Blockchain.info](https://blockchain.info/) website for each block. If you replace their values inside below code, the result which is printed out should be equal to &#8220;Hash&#8221; parameter there.

This code snippet shows a block&#8217;s header is hashed to produce a hash value which if has a specific structure, will indicate the block is valid.

<pre>#Current values here are related to block number 474650
import hashlib, struct
import time

def reverse(a):
    return "".join(reversed([a[i:i+2] for i in range(0, len(a), 2)]))

<strong>version</strong> = "00000020"
<strong>prev_block</strong> = "0000000000000000012d11c46a420474875d0e3cfcdba19aac18df597fbb6d21"
<strong>merkle_root</strong> = "2f4f3f91ce5ffa9edcf728f9574f1e5ee1f97fe6142ee612da1db14f35f0d2db"
<strong>date_time</strong> = '07.07.2017 15:22:06' 
<strong>bits</strong> = 402754864
<strong>nonce</strong> = 999740600

pattern = '%d.%m.%Y %H:%M:%S'
epoch = int(time.mktime(time.strptime(date_time, pattern)))

header_hex = (version +
reverse(prev_block) +
reverse(merkle_root)+
reverse(hex(epoch).split('x')[-1]) +
reverse(hex(bits).split('x')[-1]) +
reverse(hex(nonce).split('x')[-1]))

header_bin = header_hex.decode('hex')
hash = hashlib.sha256(hashlib.sha256(header_bin).digest()).digest()

print hash[::-1].encode('hex_codec')</pre>

[1] _<http://www.righto.com/2014/02/bitcoin-mining-hard-way-algorithms.html>_

&nbsp;