---
id: 202
title: Introduction to Cassandra Query Language (CQL) – Part 3
date: 2016-06-27T21:29:32+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=202
permalink: /blog/2016/06/27/introduction-to-cassandra-query-language-cql-part-3/
categories:
  - Uncategorized
---
In this part, I will explain important CQL commands. In previous parts ([part1](http://www.mahdix.com/blog/2016/06/10/introduction-to-cql-part-1/) and [part2](http://www.mahdix.com/blog/2016/06/19/introduction-to-cassandra-query-language-cql-part-2/)) I wrote about basic CQL concepts and data types. If you have read previous parts, you know what a keyspace or table is and how to define them.

As I said in the first part, there are two categories of CQL commands:

  1. Data Definition commands: Used to define or manipulate data structure and schema of the data
  2. Data Manipulation commands: Used to read or write data.

The first group is explained in this post. I will explain the second group in a future post.

# Data Definition commands

List of important DD commands:

  1. CREATE/ALTER/DROP KEYSPACE
  2. CREATE/ALTER/DROP TABLE
  3. USE
  4. DESCRIBE

### Keyspace related commands

You are familiar with &#8220;`CREATE KEYSPACE`&#8221; command which is used to create a new keyspace. Once a keyspace is created you can run &#8220;`DROP KEYSPACE`&#8221; to delete it.

<pre>CREATE KEYSPACE test WITH REPLICATION={'class':'SimpleStrategy', 'replication_factor':3};
DROP KEYSPACE test;
DROP KEYSPACE test2;  --this will give error because test2 does not exit
DROP KEYSPACE IF EXISTS test2; --Does nothing as test2 does not exist</pre>

The &#8220;`CREATE KEYSPACE`&#8221; command has some arguments which can be used to specify replication strategy. Replication strategy determines how and where replicas of the data will be stored when you are adding data to the tables inside the keyspace. In a future post, I will explain the different replication strategies that can be used in Cassandra. You can use &#8220;`ALTER KEYSPACE`&#8221; command to change replication strategy of a keyspace.

<pre class="pre codeblock hljs cql"><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">KEYSPACE</span> <span class="hljs-string">"test"</span> <span class="hljs-keyword">WITH</span> <span class="hljs-keyword">REPLICATION</span> =
  { <span class="hljs-string">'class'</span> : <span class="hljs-string">'NetworkTopologyStrategy'</span>, <span class="hljs-string">'datacenter1'</span> : <span class="hljs-number">3</span> };</span></pre>

Another option when creating/updating a keyspace is &#8220;`DURABLE WRITES`&#8220;. Normally Cassandra caches data in memory and flushed them to persistent storage periodically. If something wrong happens between two consecutive flush operations (e.g. machine shutdown), you will loose whatever data stored in the memory but not flushed to disk. If you enable &#8220;`DURABLE WRITES`&#8221; for a keyspace, Cassandra will use a special storage called &#8220;commit log&#8221; which will contain a copy of whatever there is in memory but not flushed to the disk. In the case of any data loss for memory-saved data, Cassandra will use the &#8220;commit log&#8221; to re-create the lost data and write them to the disk. Of course, this has it&#8217;s own load on the database. So you have to make a compromise. If data is not very important, you can disable this option but if not, you will have to enable it.

Here is an example of how to create a keyspace with durable writes disabled:

<pre class="pre codeblock hljs cql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">KEYSPACE</span> test2
  <span class="hljs-keyword">WITH</span> <span class="hljs-keyword">REPLICATION</span> = 
  { <span class="hljs-string">'class'</span> : <span class="hljs-string">'NetworkTopologyStrategy'</span>, '<span class="hljs-string">datacenter1'</span> : <span class="hljs-number">3</span> } 
<span class="hljs-keyword">  AND</span> <span class="hljs-keyword">DURABLE_WRITES</span> = <span class="hljs-literal">false</span>;</span></pre>

### Table related commands

These commands are used to create, edit or delete a table. You have seen &#8220;`CREATE TABLE`&#8221; command before:

<pre>-- create an empty table
CREATE TABLE test1 (
  field_name1 field_type1,
  field_name2 field_type2, 
...
  PRIMARY KEY (field_name1, field_name2, ...)
);

DROP TABLE test1;  -- delete the table and all of it's rows</pre>

To change a table you can use &#8220;`ALTER TABLE`&#8221; command. This command can be used to change the type of an existing column, add a new column or delete an existing column.

<pre class="pre codeblock hljs cql"><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> test1 <span class="hljs-keyword">ALTER</span> field_name1 <span class="hljs-keyword">TYPE</span> integer;

</span><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> test1 ADD field_name3 integer;

</span><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> test1 <span class="hljs-keyword">DROP field_name2;

</span></span></pre>

You can also use &#8220;`ALTER TABLE`&#8221; command to edit table&#8217;s metadata. These metadata tell Cassandra how it should handle internal storage mechanisms for this table (e.g. caching parameters, compaction settings, &#8230;).

### Other Commands

There are two other utility commands: `USE` and `DESCRIBE`.

You can use &#8220;`USE x`&#8221; to switch currently active keyspace.

You can use &#8220;`DESCRIBE keyspace1`&#8221; or &#8220;`DESCRIBE table1`&#8221; to see definition of a keyspace or a table. Note that to run the second version, you must have already switched to the keyspace which contains the table.