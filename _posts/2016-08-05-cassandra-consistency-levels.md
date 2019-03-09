---
id: 231
title: Cassandra Consistency Levels
date: 2016-08-05T01:15:12+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=231
permalink: /blog/2016/08/05/cassandra-consistency-levels/
categories:
  - Cassandra
---
In this post, I will explain more about data consistency in Cassandra. In a non-distributed system, it rarely happens that some data is missing or corrupt. But in a large scale distributed database system, this will be the norm rather than the exception. So there should be mechanisms to detect, handle and fix these situations. Customizable consistency levels are one of those mechanisms.

# Write Consistency

In the last post, I explained what happens when data is being written in Cassandra. But how Cassandra determines if a write operation is successful or not?

Each write operation can specify the consistency level it needs. This is a number which determines how many replicas have to send success reply to the coordinator so that the whole operation can be considered successful.

If for example this is set to `ONE`, with a replication factor of 3, the coordinator will ask three nodes to store the data and will return a success status upon receiving the first success response from any of these three nodes. This, in the worst case, may mean that the other two nodes have failed to write the data. This is not something which happens normally but in a large system, with a lot of nodes and a lot of data flying around, things may go wrong.

We can use the consistency level to adjust a trade-off between performance (lower consistency level = faster response time) vs. reliability (higher consistency level = prevent corrupt write).

# Read Consistency

Same as what we have for write operations, we can specify the same thing when reading data. When data is written in Cassandra, it will be written to more than one node (Refer to [this](http://www.mahdix.com/blog/2016/07/29/how-cassandra-writes-data-part-1/) and [this](http://www.mahdix.com/blog/2016/08/02/how-cassandra-writes-data-part-2/) post). Now when we want to read the data back, how should we proceed? Which of those nodes should be contacted and what if some of the contacted nodes doesn&#8217;t return a response?

The Read Consistency Level determines how the coordinator node should respond to the cases where some of the nodes, don&#8217;t reply a `READ` request or reply too late. For example, if Read Consistency is set to `ALL`, this means that coordinator should wait to get a response from all replicas. This will provide the highest level of reliability but the lowest performance. You can set it to `TWO` or `THREE` so coordinator will wait for two or three nearest replicas to return a response.

#### Example

<img class="alignnone" src="http://docs.datastax.com/en/cassandra/2.0/cassandra/images/dml_singleDCConOne.svg" alt="" width="610" height="440" /> 

Above figure shows a cluster with 12 nodes and a replication factor of 3. This means that each row of data will be written to 3 nodes (`R1`, `R2` and `R3` in this figure). When a client asks the coordinator (Node 10) to read data with Consistency Level of ONE, it contacts the nearest node (R1) for the data. In the background, it will make sure `R2` and `R3` have the most recent data and if not, a read repair operation will be initiated.

## Handling Inconsistency

If a coordinator gets different data from different replicas, which one should it pick? The answer is, Cassandra timestamps all the data so the coordinator can easily determine the most recent data which will be returned to the client. Also after this happens, the coordinator will start a read repair process in the background to make sure all replicas have up to date data.

&nbsp;