---
id: 195
title: Introduction to Cassandra Query Language (CQL) – Part 2
date: 2016-06-19T02:52:53+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=195
permalink: /blog/2016/06/19/introduction-to-cassandra-query-language-cql-part-2/
categories:
  - Uncategorized
---
In this part, I am going to give some more explanations about different data types that you can store in Cassandra. If you are familiar with programming you may know most of these data types. Here is the list of important data types:

  1. **int**: represents signed integer numbers (32-bit)
  2. **float**: floating point number (32-bit)
  3. **double**: floating point number (64-bit)
  4. **boolean**: can only hold true or false
  5. **text**: represents a string of characters
  6. **blob**
  7. **counter**
  8. **list**
  9. **map**
 10. **set**

The first five data types are almost familiar for most people because they are used in programming languages and other databases too. But maybe the second half of the list is not quite clear. So I will explain them with examples.

### &#8216;blob&#8217; data type

This data type is used to store a very large chunk of data bytes. Mostly this data type is used to store files on the database.

### &#8216;counter&#8217; data type

This data type is basically a 64-bit integer number. The main differences are:

  * You have to define dedicated tables for counter columns. This means that if you want to have a counter column, you will need to define a new table containing only primary key and the counter column you want. You cannot add non-counter fields which are not part of primary key.
  * This data type is optimized for concurrent updates. That&#8217;s the main difference between `counter` and `integer`. If you have an int column, to update its value, the database needs to lock, read the current value, update value and unlock. This is quite a heavy processing operation. But when you use counter, there will be no locking mechanism and there is no need to read current value before doing the update.

### Collection data types

Cassandra supports collection data types. These are data types that &#8216;contain&#8217; other non-collection data types.

A &#8216;`list`&#8216; can be defined like &#8216;`list<text>`&#8216; or &#8216;`list<boolean>`&#8216; and indicates an array or vector of zero or more values of that types.

&#8216;`map`&#8216; means a relationship between two item which are called key and value. We can say that &#8216;key&#8217; is mapped to &#8216;value&#8217;. For example, suppose that we have a list of U.S. states and their population. We can then say that &#8216;state name&#8217; is mapped to &#8216;population&#8217;. Hence, when we need the population of a state we can easily use this list to figure that out. Here &#8216;state name&#8217; is key and &#8216;population&#8217; is value. We can define a map by specifying the data type of its key and value items, like &#8216;`map<text, int>`&#8216;.

&#8216;`set`&#8216; is exactly the same as &#8216;list&#8217; with one exception: it cannot have duplicate values.

### Example1

<pre>CREATE TABLE table1 (
key1 text PRIMARY KEY,
population_map map&lt;text, int&gt;,
list1 list&lt;int&gt;,
set1 set&lt;boolean&gt;
);

INSERT INTO table1 (key1, population_map, list1, set1) 
VALUES ('mahdi', {'NY': 10, 'CA':30, 'TX': 6}, [1, 2, 3], {true, false, false});</pre>

Above statements create a table named `table1` containing 4 columns (We assume keyspace is already created and activated). The first column is the primary key of the table (Each table must have a primary key) and remaining 3 columns are of different collection types which were introduced.

Next statement inserts a single row into this table. Note the syntax used to specify a value for a map, list, and set.

Using a combination of collections, you can define quite complex data structures in a table in Cassandra. The reason for having complex data types is that in Cassandra everything is optimized for a lot of write operations and a few reads. Hence, it is better if you can read everything that you need with a single query on a single table rather than multiple queries across multiple tables. In contrast, in traditional RDBMSs, you will scatter columns onto multiple tables with relations to each other and when you want to read, you can join all those tables together to read multiple rows in a single query.

### Example2

<pre>UPDATE table1 SET population_map['NY'] = 12 
WHERE key1 = 'mahdi';

DELETE population_map['TX'] FROM table1
WHERE key1 = 'mahdi';</pre>

### Used-defined data types

You can define your own data type with its own attributes in Cassandra. If you are familiar with C or C++ this is same as &#8216;`struct`&#8216;. You will then be able to use that data type as the type of columns when you define tables.

Below sample shows how to define a custom data type and use it when creating a new table and when inserting (or updating) data of the new table.

<pre>CREATE TYPE person_info(
                    name text,
                    family text,
                    age int
);

CREATE TABLE student(
                    student_id text PRIMARY KEY,
                    personal_info person_info
);                  

INSERT INTO student (student_id, personal_info) VALUES 
('A19201', { name: 'mahdi', family: 'mohammadi', age: 32 });

UPDATE student SET personal_info.age = 31 where id = 'A19201';

</pre>

In the next part, I will explain more about CQL commands.