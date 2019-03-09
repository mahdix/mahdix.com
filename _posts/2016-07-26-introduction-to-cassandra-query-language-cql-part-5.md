---
id: 218
title: Introduction to Cassandra Query Language (CQL) – Part 5
date: 2016-07-26T04:50:20+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=218
permalink: /blog/2016/07/26/introduction-to-cassandra-query-language-cql-part-5/
categories:
  - Uncategorized
---
In this part, I will explain the two remaining CQL commands: `UPDATE` and `DELETE`.

## DELETE command

As the name suggests, this command is used to delete data from a table. The structure of the command is very similar to `SELECT` command. You can specify a condition determining which rows you want to delete.

<pre>DELETE FROM table1;
DELETE FROM table2 WHERE user_id = 'mahdix';</pre>

The first command will remove all rows from table `table1`. The second command will only remove rows whose `user_id` is matching the given criteria.

Note that you can also use `keyspace_name.table_name` syntax instead of the table name.

In Cassandra, the relation between rows and columns is looser than in traditional RDBMSs. Because of that, each row can have its own columns which can be same as other rows or different. You can use `DELETE` command to delete some columns from a row. This will not remove the whole row but some columns will be removed without touching other remaining columns. For example:

<pre>DELETE field1, field2 FROM table1 WHERE name = 'mahdi';</pre>

This will delete two fields from rows in `table1` which match the given criteria.

If you want to delete all rows from a table, there is a simpler command for that:

<pre>TRUNCATE keyspace1.table1;</pre>

## UPDATE command

This command is used to change values of attributes for one or more rows:

<pre>UPDATE table1 SET age = age + 1 WHERE name = 'mahdi';</pre>

This command will update all rows in `table1` who have &#8216;`mahdi`&#8216; as the value of their name field. This update statement will increment the value of `age` attribute.

To change value of set collection data types you should use plus and minus sign:

<pre>UPDATE table1 SET emails = emails + { 'mahdix@gmail.com' } WHERE name = 'mahdi';
UPDATE table1 SET emails = emails - { 'info@mahdix.com' } WHERE name = 'mahdi';
UPDATE table1 SET emails = {} WHERE name = 'mahdi';

</pre>

Above syntax is for set data type. For a list, follow the same syntax, except using brackets instead of braces:

<pre>UPDATE table1 SET members = ['person1', 'person2'] WHERE id = 1;
UPDATE table1 SET members = members + ['person3'] WHERE id = 3;
UPDATE table1 SET members = ['person4'] + members WHERE id = 4;
UPDATE table1 SET members = members - ['person1'] WHERE id = 2;</pre>

As you can see, you can either append or prepend data to a list. This is doable because, in a list, the order of items is important (unlike a set where the order or repetition does not matter).

For map data type, you follow the same syntax used for insertion of data:

<pre>UPDATE table1 SET state_population = { 'OH': 8, 'CA': 30} WHERE code = 'US';
UPDATE table1 SET state_population['OH'] = 9 WHERE code = 'US';
UPDATE table1 SET state_population = state_population + { 'TX': 17 } WHERE code = 'US';</pre>

## Other commands

There are some other commands which are not discussed in my posts. Mostly because they are not very common. For example, there are permission control commands (`CREATE USER, ALTER USER, GRANT, REVOKE`) to manage users and their permissions (such as `CREATE, DROP, SELECT, MODIFY`, &#8230;) to a table or keyspace.

Another useful set of commands is for working with index (`CREATE INDEX, DROP INDEX`). Indexes are used to help Cassandra find rows faster. If you have some columns which are not part of primary key but are commonly used in your queries&#8217; criteria, they may be a good candidate to create an index. In future posts, I will explain more about indexes.