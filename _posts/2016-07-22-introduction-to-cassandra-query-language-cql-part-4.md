---
id: 214
title: Introduction to Cassandra Query Language (CQL) – Part 4
date: 2016-07-22T16:44:45+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=214
permalink: /blog/2016/07/22/introduction-to-cassandra-query-language-cql-part-4/
categories:
  - Uncategorized
---
After discussing CQL commands for Data Definition in this post I am going to explain &#8220;Data Manipulation&#8221; commands. These commands are used to add new data to a table or read/update/delete data.

Each of above four types of actions has it&#8217;s own command:

  1. ****INSERT**:** Is used to add new a row to a table,
  2. **SELECT**: Is used to read one or multiple rows from a table,
  3. **UPDATE**: Used to change values of rows in a table,
  4. **DELETE**: To delete one or more rows.

## INSERT command

Suppose that we have a keyspace and a table in our Cassandra database. Here is how we can insert a single row into the table:

<pre>INSERT INTO mytable (field1, field2, field3) VALUES ('value1', 'value2', 'value3);</pre>

Above command will set value for three fields in the `mytable` table. It&#8217;s a common practice to capitalize all CQL keywords but it&#8217;s not necessary. It&#8217;s assumed that all fields have text type. Let&#8217;s see how to insert data when fields have other types (If you are not familiar with different data types in Cassandra, you can refer to this [post](http://www.mahdix.com/blog/2016/06/19/introduction-to-cassandra-query-language-cql-part-2/)).

<pre>INSERT INTO mytable (text_field, set_field, map_field, list_field) VALUES 
(
   'this is a text', 
   {'set_element1', 'set_element2', 'set_element3'}, 
   {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'},
   ['list_element1', 'list_element2', 'list_element3']
)</pre>

As you can see, the syntax used to specify values for collection data types is pretty intuitive.

Some notes about `INSERT` statement:

  * You can specify keyspace name and table name altogether, so you don&#8217;t need to USE the keyspace before inserting data (`INSERT INTO` keyspace1.table1 &#8230;)
  * You can set a TTL when inserting data. TTL means Time To Live and specifies the seconds after which, the values you are inserting will have to be deleted. So for example:

<pre>INSERT INTO table1 (field1) VALUES ('temp_data') USING TTL 10;</pre>

After 10 seconds, this row will be removed. You can query TTL For a column using below syntax:

<pre>SELECT TTL(field1) FROM table1;</pre>

Above command will return current TTL of `field1` for all rows of the table.

## SELECT command

This command is used to read (or select) one or more rows from a table. The simplest version of this command is:

<pre>SELECT * FROM table1;
SELECT * FROM keytspace1.table1;</pre>

As with the `INSERT` command, here you can either use table name or keyspace name + table name to indicate the target. Above commands will return all the rows that we have in table1 table.

But what if you need a special row or rows? There is a keyword, &#8220;`WHERE`&#8221; which you can use to describe common properties of the rows you are looking for:

<pre>SELECT * FROM table1 WHERE id=12;
SELECT * FROM table2 WHERE id=12 AND age &lt; 5;
SELECT * FROM table3 WHERE state='OH' or age &gt; 30;</pre>

Above three commands, will return a set of rows according to given conditions. The syntax used to describe condition is pretty straightforward. I will explain some more advanced examples of `SELECT` command below:

<pre>SELECT * FROM table1 WHERE id IN (10, 11, 12);
SELECT * from table1 WHERE id = 10 OR id = 11 OR id = 12;</pre>

Above two commands are exactly the same and will returns rows that their &#8216;`id`&#8216; field either has a value of 10, 11 or 12.

<pre>SELECT * FROM table1 WHERE emails_set CONTAINS 'mahdix at gmail.com';
SELECT * FROM table2 WHERE map_field1 CONTAINS 'The Value1' OR map_field1 CONTAINS KEY 'OH';

</pre>

Note that if you are filtering `SELECT` output using a condition which relies on a non-primary-key field, then this will be considered an expensive query and Cassandra will refuse to run it unless you have included &#8220;`ALLOW FILTERING`&#8221; at the end of the query.

<pre>SELECT * FROM tbl_people ORDER BY age DESC LIMIT 100;</pre>

The above query will return top 100 oldest people who have a record in our &#8216;`tbl_people`&#8216; table.

I try to limit my posts to 500 words, that&#8217;s why I will explain &#8216;`UPDATE`&#8216; and &#8216;`DELETE`&#8216; statements in another post 🙂