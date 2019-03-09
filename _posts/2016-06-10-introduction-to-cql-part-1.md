---
id: 183
title: 'Introduction to Cassandra Query Language (CQL) &#8211; Part 1'
date: 2016-06-10T04:38:44+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=183
permalink: /blog/2016/06/10/introduction-to-cql-part-1/
categories:
  - Uncategorized
---
Cassandra has it&#8217;s own language when you want to talk to it. This language is called CQL (Cassandra Query Language). If you are familiar with SQL (Structured Query Language) then this post will be very familiar to you because CQL is designed to be very similar to SQL.

In this post I will start with an introduction to the concept of database and it&#8217;s parts. Then I will explain some of the most important commands in CQL.

Basically, Cassandra (and most other database systems) organizes data in a way that is more intuitive and easy to retrieve. The highest level of this organization is named `keyspace` (same as a database in traditional database systems). You can think of a keyspace as a container into which you store all data that are related to each other. Normally this means all data of a single application.

Inside a keyspace, there are a number of tables. You can think of a table as an excel spreadsheet which has rows and columns. Each row represents a single piece of information. Each column means a specific attribute you wish to store in that table. For example, if we define a table to store student scores, its columns would be &#8220;student\_id&#8221;, &#8220;student\_name&#8221; and &#8220;score&#8221;. Each row would represent a single score of a student. Note that each column is capable of storing a specific type of data. Some columns are designed to store numbers while other may only store texts.

Generally, there are 2 types of actions we can do with a database:

  1. Working with data structures (keyspace, table, column)
  2. Working with data itself

The first group of commands is called DDL (Data Definition Language). The second group is called DML (Data Modification Language).

Obviously, without some place to store your data, you cannot do anything! so first of all you will need to create a data structure to support storage of your data. This structure will contain keyspaces, tables and columns. Normally you will define this structure to reflect the business requirements of your application (But in case of Cassandra there are other factors determining the structure of your data about which I will talk later).

I believe in &#8220;explanation by example&#8221;. So, as a very basic example, let&#8217;s create a keyspace and a table and insert some data into this table. Below are the commands that you need to execute:

<pre> CREATE KEYSPACE test1 WITH REPLICATION  = { 'class' : 'SimpleStrategy', 'replication_factor' : 1 };

USE test1;
<code>CREATE TABLE users (
 userid text PRIMARY KEY,
 first_name text,
 last_name text,
 age int
);
</code>INSERT INTO users (userid, first_name, last_name, age) VALUES ('12345', 'mahdi', 'mohammadinasab', 32);

SELECT * FROM users;</pre>

&#8220;_How can I run these commands?_&#8220;, you may ask. I assume you have downloaded and installed Cassandra. If you have not done so, please refer to [Getting Started](https://wiki.apache.org/cassandra/GettingStarted) .

After starting Cassandra, you will need to run the `cqlsh` utility. This utility is located inside `'./bin'` directory.

Explanation for three commands:

  1. **CREATE KEYSPACE**: This command will create a new keyspace for you. Remember that all data is organized in keyspaces so you cannot define data structure or read/write data unless you have at least one keyspace. Note that CQL commands end with a semicolon. In next parts I will explain about the &#8216;`WITH REPLICATION`&#8216; section of the command.
  2. **USE test1**: After creating the initial and only keyspace you will need to select it for next commands. This command activates the &#8216;`test1`&#8216; keyspace so future commands will be running inside this keyspace and won&#8217;t affect any other keyspace. Note that after running this command the cqlsh prompt changes to denote current active keyspace.
  3. **CREATE TABLE**: Although you have a keyspace, you cannot read or write anything from/to the keyspace. It&#8217;s because keyspace is not used to organize your data directly. You have to put them inside tables which in turn are located inside a keyspace. This command will create a simple table named &#8216;`users`&#8216; which has 4 attributes (columns): `userid, first_name, last_name` and `age`. Notice the use of underscore in field names? That&#8217;s because you cannot have spaces in the name of a column. For each column you will specify the type of the data it will accept. Later I will explain about the `PRIMARY KEY` option of this command.
  4. **INSERT INTO: **This command writes data into a table. You need to specify a table name, columns you want to write values for and provide corresponding values. This will insert a new record in users table under `test1` keyspace. Note that for text fields, you will need to enclose values within single quotes.
  5. **SELECT**: This command is used to read all of the rows from a table. &#8216;*&#8217; means you want to read values for all columns. When you run this command, it will output data you have written in the previous step.

So this is the most basic set of commands you could execute. You created a simple data structure, wrote some data in it and read the data back. Try to experiment with these commands. Create multiple keyspaces and tables. Write data and read them back.

In the next part I will explain different data types in Cassandra.

&nbsp;