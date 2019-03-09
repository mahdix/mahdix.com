---
id: 237
title: 'Cassandra Internals: Bloom filters'
date: 2016-08-08T14:01:38+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=237
permalink: /blog/2016/08/08/cassandra-internals-bloom-filters/
categories:
  - Cassandra
---
In a previous [post](http://www.mahdix.com/blog/2016/08/02/how-cassandra-writes-data-part-2/) I explained about SSTable and their role in the persistent storage of data in Cassandra.

Suppose that following query is received on a node (`news_id` is primary key):

<pre>SELECT * FROM tblNewsItems WHERE news_id = '8bacfe891fa89bfab98d9e99f9a9';</pre>

How can the node determine if this specific row exists in it&#8217;s local data? As we know we have in-memory tables (mem-table) but they are used as a caching mechanism and not the real storage. So any data lookup in these in-memory storages won&#8217;t be enough as they don&#8217;t have the whole range of data.

Another solution is to read the SSTable and check if the table contains the key that we want. Even in case of using effective search algorithms like binary search, this will be too expensive because it involves a lot of disk reads which may not be efficient enough for a system with a lot of data and a lot of read queries.

The solution is to use [Bloom Filter](https://en.wikipedia.org/wiki/Bloom_filter) data structure. This is an in-memory, probabilistic data structure which can help you find if an element is member of a set or no? Here the set is the Cassandra table (which is actually a set of rows) and the element is the primary key we are looking for. So we have these features:

  1. It is in-memory, so it will be fast and efficient.
  2. It is probabilistic means the result will not be guaranteed to be correct. There is a high chance that the result is correct and this is enough for us. This is the trade-off that we pay to have such an efficient in-memory data structure. As a result, if the bloom filter says a row is inside a set, there is a very low probability that this is not correct. But the other side of the result is guaranteed to be true. If it says some row ir NOT in the set, it definitely is not.

## How bloom filter works

A bloom filter is a bit-map containing `n` bits and a set of hash functions which are expected to be independant. Let&#8217;s assume we have a bloom filter with 100 bits and 3 hash functions (`f1`, `f2` and `f3`). In practice the size of bloom filter will be much larger (MB or GBs). Each of has functions will receive an arbitrary input (row primary key) and output a number between 1 to `n`. You can easily achieve this output by using modulo operator and common hash functions.

There are two algorithms for insertion of data into a bloom filter and checking whether something exists in the set or no. These alrogithms are pretty simple and straightforward. To insert data `x` into bloom filter:

  1. Apply hash functions on `x`. So we will have `a=f1(x)`, `b=f2(x)` and `c=f3(x).`
  2. Set bits numbered `a`,`b` and `c` in the bloom filter to one (Note that they may have already been set to one but this doesn&#8217;t matter).

To check if data `x` exists in the bloom filter:

  1. Apply hash functions on y: So we will have `d=f1(y)`, `e=f2(y)`, `f=f3(y).`
  2. Check bits numbered `d`, `e`, and `f` in the bit-map of bloom filter. Are they all set to one? If so, the row probably exists in the set. If even one of those bits is zero, means that row definitely does not exist in the set.

## How false positives are handled?

False positive for a bloom filter means cases where the filter indicates that a row exists in the table but it was not. Remmber that this is a probabilistic data structure so these cases may happen.

If there is a false positive, we won&#8217;t know about it until we scan the SSTable. So generally Cassandra will scan the SSTable looking for a specific row, if the bloom filter indicates the row exists in the table. If after scan completion, row is not found, this will be recorded as a false positive.

You can run &#8216;`nodetool cfstats`&#8216; command in a Cassandra node to view a lot of statistics about the node. One of those stats is for bloom filters which shows you the memory consumed by bloom filter and number of false positives.

&nbsp;