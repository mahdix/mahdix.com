---
id: 223
title: 'How Cassandra Writes Data &#8211; Part 1'
date: 2016-07-29T00:42:59+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=223
permalink: /blog/2016/07/29/how-cassandra-writes-data-part-1/
categories:
  - Cassandra
---
As you know, Cassandra is a distributed database. This means that the Cassandra application will be running on multiple machines. We call each running application, a Cassandra instance and each of those machines, a host machine (or node). The collection of these host machines is called a Cluster.

Instances on host machines, always communicate to each other, sending and receiving data. The data and operations on them are distributed between hosts based on this ongoing communication. The details of this distribution depend on the operation which is taking place.

In this post (and the follow-up post), I am going to explain what exactly happens when an `INSERT` statement is executed on a Cassandra cluster. Of course, this post assumes that a cluster is already set-up and all nodes know each other. The explanation of the set-up process needs it&#8217;s own blog post which I will do in future.

First I am going to explain some of the terms I will need:

  1. **Client**: This is the machine, on which the `INSERT` command is issued. We assume this machine is connected to one of the nodes in the cluster and sends the command to the node to be executed. This can be any node in the cluster. There is no special &#8216;`master`&#8216; node in the cluster, all of them are the same (from a client&#8217;s perspective).
  2. **Coordinator**: This is one of the nodes in the cluster that is being contacted by a client. It is responsible for inserting data into Cassandra database and communicate with other nodes in the cluster to do their job and finally returning the result to the client.

## What is replication?

One feature of Cassandra is it&#8217;s replication capabilities. This means that any data that you write to Cassandra can be stored on multiple nodes in the cluster. You have the option to determine how many nodes should store copies of the data and how they are chosen.

So if replication factor is 3, inserted data will be written to persistent storage on 3 different nodes. The advantage of this is that in case of failure (in network, hardware, &#8230;) the system can handle the situation and return correct data in a timely manner to the client.

You can set-up different replication strategies which determine replica nodes for data. For clarity, we assume that replication strategy is set to replicate data to 3 nodes in the cluster. There are more advanced replication strategies explained [here](https://docs.datastax.com/en/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html).

## Primary key, Cluster key and Partition Key

When you create a table, you specify one or more columns as the primary key. If you specify only one column, you will have a simple primary key. If you specify two or more columns, it will be a called a compound primary key.

<pre>CREATE TABLE tbl1 (user_id int, name text, PRIMARY KEY (user_id));
CREATE TABLE tbl2 (user_id int, ssn text, name text, PRIMARY KEY (user_id, ssn))</pre>

In the above example, the first table (`tbl1`) has a simple primary key while the second one has a compound primary key.

When we have a compound primary key, it will have two parts: Partition key and Cluster key. If you do not specify which fields belong to which part, by default, the first field of the primary key will be considered partition key and the rest will be cluster key (obviously, a simple primary key has no cluster key). In the above example, for `tbl2`, `user_id` is partition key and `ssn` is clustering key.

<pre>CREATE TABLE tbl3 
(user_id int, ssn text, region int, order_number int, name text, 
PRIMARY KEY ((user_id, ssn), region, order_number);</pre>

In the above example, we have a table with a compound primary key (it has 4 fields). The `user_id` and `ssn` pair is the partition key and `region`, `order_number` are clustering key.

In the next post I will explain how these keys are used when writing data.

## 

&nbsp;