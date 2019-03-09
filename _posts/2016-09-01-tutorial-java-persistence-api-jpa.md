---
id: 246
title: 'Tutorial: Java Persistence API (JPA)'
date: 2016-09-01T12:53:14+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=246
permalink: /blog/2016/09/01/tutorial-java-persistence-api-jpa/
categories:
  - Uncategorized
---
&nbsp;

Java Persistence API or JPA is a standard specification and a set of classes which helps developers write a provider-independent persistence layer in his applications. JPA has taken best ideas from different persistence providers (e.g. Hibernate, TopLink, etc.).

The general workflow for JPA is:

  1. Define POJO classes corresponding to database entities. You annotate these classes to let JPA find them at runtime.
  2. Add `persistence.xml` file which explains about your POJO classes and database connection information.
  3. Write the code to work with database entities (CRUD operations) using JPA classes and JPQL language (which is similar to SQL).

## Defining Entities

Entity classes are simple Java classes. These classes don&#8217;t need to inherit from a specific class. All you need to do is annotate the class, add relevant fields representing database table fields and adding getter/setter methods.

You can also annotate fields to indicate primary key or give some more information about database representation of the field.

Below is example of a student entity class:

<pre class="prettyprint prettyprinted"><code>&lt;span class="kwd">import&lt;/span>&lt;span class="pln"> javax&lt;/span>&lt;span class="pun">.&lt;/span>&lt;span class="pln">persistence&lt;/span>&lt;span class="pun">.*;

@Entity(name = "TBL_STUDENT")
public class Student {
    @Id
    @Column(name="ST_ID", nullable=false)
    @GeneratedValue
    private long id;

    @Column(name="FIRST_NAME")
    private String firstName;

    @Column(name="LAST_NAME")
    private String lastName;

    @Column(name="AVG_SCORE")
    private float avgScore;

    //getter and setter for fields go here
}&lt;/span></code></pre>

Note that we use Java annotations to represent metadata explaining more information about the entity.

  * **@Column** annotation is used to define database field name of the class field. You can omit this is names are the same.
  * **@Entity** is used to declare this class as a DB entity.
  * **@Id** is used to represent primary key of the entity
  * **@GeneratedValue** is used to declare JPA provider should automatically provide value for this field upon data insertion.

JPA has a lot of other annotations to explain the type of data that can be stored in a field, table inheritance, composite primary key, entity relationships and much more. These will be explained in a future post.

## Adding persistence.xml

In this XML file, you declare some metadata which cannot be written in the code easily. This is a sample XML file:

<pre>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;persistence&gt;
 &lt;persistence-unit name="<strong>mytest</strong>" transaction-type="RESOURCE_LOCAL"&gt;
  &lt;<strong>provider</strong>&gt;org.eclipse.persistence.jpa.PersistenceProvider&lt;/provider&gt;
  &lt;class&gt;test.Student&lt;/class&gt;
  &lt;properties&gt;
  &lt;property name="javax.persistence.jdbc.driver" value="org.sqlite.JDBC" /&gt;
  &lt;property name="javax.persistence.jdbc.url" value="jdbc:sqlite:sample.db" /&gt;
  &lt;property name="eclipselink.ddl-generation" value="drop-and-create-tables" /&gt;
  &lt;property name="eclipselink.ddl-generation.output-mode" value="database" /&gt;
  &lt;/properties&gt;
 &lt;/persistence-unit&gt;
&lt;/persistence&gt;</pre>

The XML file structure is almost the same across different projects. I have highlighted two important parts of the file:

  * name of the persistence-unit is a unique string which is used in the code to refer to this XML file.
  * provider XML tag is used to indicate which provider will be used at runtime to talk to the database.

The properties section of the XML file contains database connection information (e.g. database driver type, database name, &#8230;).

## The code

The last step is using JPA classes to get a connection to the database and working with your POJO classes and EntityManager to do CRUD operations.

The basic flow is:

  1. Getting an instance of EntityManagerFactory class through Persistence class. Here you specify the persistence-unit name from XML file.
  2. Getting an instance of EntityManager class through EntityManagerFactory.
  3. Working with EntityManager instance and it&#8217;s EntityTransaction instance to create, read, update or delete POJO classes.

Here is the basic code:

<pre>//step 1
EntityManagerFactory factory = Persistence.createEntityManagerFactory("mytest");
//step 2
EntityManager entityManager = factory.createEntityManager();
//step 3
EntityTransaction txn = entityManager.getTransaction();
txn.begin();

//step 4 - CRUD operations
...

//step 5 - finalization
txn.commit(); //you need this line if you have changed some db data
entityManager.close();
factory.close();</pre>

## CRUD operations

The CRUD operations become pretty straightforward considering the fact that you just need to work with your POJO classes as a proxy to the database table rows.

  * **Create**: You instantiate POJO class, set field values, call persist method of EntityManager.
  * **Read**: You create a JPQL query and get the result as a `List<POJOClass>`.
  * **Find**: Calling `find` method on EntityManager and giving the primary key value of the row. The result will be an instance of a POJO class.
  * **Update**: First find the row, update the POJO class fields.
  * **Delete**: Find the row, then call `EntityManager.remove` method.

Below is sample code for CRUD operations:

<pre>//CREATE a new row
Student st = new Student();
st.setFirstName("mahdi");

entityManager.persist(st);

//UPDATE row
Student st = entityManager.find(Student.class, 19);
st.setLastName("Mohammadinasab");

//DELETE row
Student st = entityManager.find(Student.class, 19);
entityManager.remove(st);

//READ rows
Query q = entityManager.createQuery("SELECT s FROM Student s");
List&lt;Student&gt; students = q.getResultList();</pre>

Note that in the update process you don&#8217;t need to call any JPA method. Just update POJO fields and when you commit the transaction, data will be updated.

For the query which reads rows from a database, JPQL language is used. I will explain more about JPQL syntax and how to handle relationships (one-to-many and many-to-many) in a future post.

&nbsp;