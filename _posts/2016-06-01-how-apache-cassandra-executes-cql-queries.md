---
id: 176
title: How Apache Cassandra executes CQL queries?
date: 2016-06-01T20:54:35+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=176
permalink: /blog/2016/06/01/how-apache-cassandra-executes-cql-queries/
categories:
  - Uncategorized
---
Apache Cassandra is a distributed, highly scalable database which organizes data into rows and columns (Somehow like traditional DBs). Each row must have a unique primary key. This key is used to distribute the data to the DB nodes. If you are not familiar with Apache Cassandra please refer to [cassandra.apache.org](http://cassandra.apache.org/).

I have been a member of Cassandra developer community and I would like to shed some lights on the query execution process from a developer&#8217;s perspective.

The query language of Cassandra is called CQL (Cassandra Query Language). Although it is much similar to SQL language, it has a lot of underlying differences. You can use \`cqlsh\` tool to run CQL commands against a Cassandra system or you can run queries from source code (using one of Cassandra drivers to talk to a Cassandra instance).

When you submit a command from \`cqlsh\` tool, it is sent to Cassandra over a network connection. On the other side of the connection, a daemon is waiting for commands. It will produce a response for each received commands and send it to the originator. Cassandra uses [Netty](http://netty.io/) for its network protocol handling. Netty allows Cassandra to define complex message encoding, decoding and dispatching to handle the communication protocol with its clients.

Here is the general overview of the flow of method calls during processing of a CQL query:

<a href="http://www.mahdix.com/wp-content/uploads/2016/05/Cassandra.png" rel="attachment wp-att-179"><img class="alignnone size-full wp-image-179" src="http://www.mahdix.com/wp-content/uploads/2016/05/Cassandra.png" alt="Cassandra" width="1184" height="1166" /></a>

  1. The starting point, on the server side, is _CassandraDaemon_ which initializes the service and _Server_ which will be network endpoint to receive requests from clients. When you run &#8216;_./bin/cassandra_&#8216; it will invoke JVM to run &#8216;_main_&#8216; method of this class. This will start the initialization process for Cassandra service and then set up _NativeTransportService_. This service will configure and start the network service (Defined in _Server_ class).
  2. _org.apache.cassandra.transport.Server_: This class is responsible for setting up a socket server. According to Netty terminology, it defines a &#8216;childHandler&#8217; which will be responsible for setting up and initialize each newly established Channel (network connection). This is done by an internal static class, named Initializer (If Cassandra is set up to use SSL mode, this will be _SecureInitializer_). This is the component which receives a CQL query request, delegates the processing and sends the response.
  3.  _org.apache.cassandra.transport.Server.Initializer_: Upon establishment of a new connection with a client, this class will configure the pipeline for processing incoming and outgoing messages through this channel. This includes frame encoding/decoding, compression/decompression and message encoding/decoding. Additionally, it will add a message dispatcher to the pipeline (An instance of Message.Dispatcher) which is responsible for handling incoming messages, delegate execution to corresponding modules and produce an appropriate response.
  4. _org.apache.cassandra.transport.Message.Dispatcher_: All network related classes act on Request and Response classes. Requests in Cassandra are all based on _org.apache.cassandra.transport.Request_ abstract class. For each different type of request, there is a separate concrete implementation of this class. The most important of which for this post is _org.apache.cassandra.transport.messages.QueryMessage_. Message dispatcher class will extend _SimpleChannelInboundHandler_ from Netty library. The main implemented method here is &#8216;_channelRead0_&#8216;. This method is called when a new message is received (and processed through the configured pipeline). This method calls &#8216;execute&#8217; method of the received request. The output of this method will be a Response which will be sent back to the client.
  5. _org.apache.cassandra.transport.Message.Request$execute_: This is an abstract method which is implemented for different types of messages. The message type in which we are interested is QueryMessage.
  6. _org.apache.cassandra.transport.messages.QueryMessage.execute_: This method delegates the processing to _QueryProcessor.process_ method. The only logic here is for tracing and exception handling.
  7. _org.apache.cassandra.cql3.QueryProcessor$process_: This method first calls &#8216;_getStatement_&#8216; to parse the query string (using ANTLR parser) and create an instance of &#8216;_ParsedStatement.Prepared_&#8216;. This will include a &#8216;_statement_&#8216; attribute which is of type _CQLStatement_. _CQLStatement_ is an interface which has implementations for each query type. For example _org.apache.cassandra.cql3.statements.UpdateStatement_ handles INSERT/UPDATE statements. After getting an instance of CQLStatement, its &#8216;_execute_&#8216; method will be invoked to do the processing and return the response.

&nbsp;