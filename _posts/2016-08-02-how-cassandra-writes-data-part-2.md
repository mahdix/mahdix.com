---
id: 226
title: How Cassandra Writes Data – Part 2
date: 2016-08-02T00:49:46+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=226
permalink: /blog/2016/08/02/how-cassandra-writes-data-part-2/
categories:
  - Cassandra
---
This is the second part in a two-part series about the internals of Apache Cassandra for writing data. Click [here](http://www.mahdix.com/blog/2016/07/29/how-cassandra-writes-data-part-1/) to read the first part.

## Flow of execution

The process begins by a client sending a request to the coordinator containing an `INSERT` statement. Let&#8217;s assume we have a table named `table1` according to below definition:

<pre>CREATE TABLE table1 (id int, name text, PRIMARY KET id);</pre>

And the cluster consists of five nodes, which we will call `node1, ..., node5`, and the replication factor is 3. The cluster will have a partitioner algorithm which given the primary key of the row, outputs a big random number. We call this random number, the identifier of the row. We assume an identifier number is a 128-bit number which means it will be between 0 and 2^128-1 (`max` number).  Now consider this as a range of `(0, max)`. Upon cluster configuration, this range is divided into five equal sub-ranges (because the cluster has five nodes):

`R1=(0, max/5), R2=(max/5+1, 2*max/5), ...` .

Each node will have its own range. `R1` will be assigned to `node1`, `R2` to `node2` etc.

All nodes in the cluster know about other nodes and their corresponding range.

Coordinator receives this CQL statement:

<pre>INSERT INTO table1(id, name) VALUES (100, 'mahdi');</pre>

First, it applies the partitioner algorithm on &#8216;`100`&#8216; (the primary key). The result will be a number. It then determines the corresponding range `(Ri, i=1,..,5)` within which the number lies. Let&#8217;s assume the result number lies within `R2` range. So `node2` will receive the first copy of the row. But we have a replication factor of 3 which means data needs to be stored on three different nodes. In a simple replication strategy, additional nodes will be next nodes after the original receiver of the data which are `R3` and `R4` in our example. So coordinator will send 3 requests to `R2`, `R3` and `R4` to store the values for the new row in their local data (There is another more complex strategy called NetworkTopologyStrategy, see [here](https://docs.datastax.com/en/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) for more information).

Note that `R1` and `R5` know nothing about the new row but the whole cluster contains the new data and you can later query this data from any of the nodes in the cluster.

## SSTable, commit log and memtable

In a node which belongs to a Cassandra Cluster, there are 3 components which are used to store data:

  * **SSTable**: The storage of database on the persistent disk (e.g. Hard Disk). When data is written to this storage, it is permanently persisted, but problem is, writing to this storage is expensive. That&#8217;s why we need the other two components.
  * **Memtable**: For each table in the database, there is a memory space allocated to store its data. It is extremely fast because it&#8217;s in memory but it is not reliable, because in case of a problem in the node, all it&#8217;s data will be cleared.
  * **Commit log**: This is a persistent data file written to the local storage which contains a copy of all the actions applied on the database. These data can be used to re-construct SSTable in case of a problem on the node.

When writing data to the database, the data is written on Memtable and then on Commit log. After that a successful response is sent to the requester indicating the write operation is done successfully. Note that data is not written to SStable but it&#8217;s on persistent storage (Commit log) so it is safe. Periodically, the node requests memtables to be flushed to SStable which will write all updates to SStable (the final permanent storage for the data).

##