---
id: 249
title: What is Apache Maven and why do we need it?
date: 2016-09-02T17:17:30+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=249
permalink: /blog/2016/09/02/what-is-apache-maven-and-why-do-we-need-it/
categories:
  - Uncategorized
---
I was going to write a blog post about Spring framework but I thought maybe it&#8217;s better to start with Maven as the first step.

With an increase in the number of Java libraries and frameworks and projects, there was a need for a standard way of describing how a piece of software should be built and what are it&#8217;s dependencies. Normally a Java project is using a lot of frameworks and tools (e.g. Logging, Database, Encryption, Math, &#8230;). We need a standard (machine-readable) way of describing these dependencies and how to compile and build the project.

Using Maven enforces a specific directory structure to your project. There is a specific directory path for your source code, runtime resource files, &#8230; Another change in your project is adding a file named pom.xml which will describe dependencies and build options.

I will explain some basic concepts in the Maven world, then will proceed to create a new project based on Maven.

## Artifact

An Artifact, is a JAR file which is available for use in a Java project. Artifacts can be downloaded from Maven Repository (a publicly available website), but normally this is done automatically by maven command line.

Each Artifact represents a small piece of software that can be re-used in a software project. For example, there are artifacts for logging and encryption. There are a number of Maven repositories available, but the well-known repository is the one provided by Maven itself which is available for search and browse at <http://search.maven.org/>. You can search for artifacts and download them. You also have a local repository which contains JAR files downloaded from remote repositories or installed locally.

Each artifact is uniquely identified by using 3 elements: group-id, artifact-id and version.

Group-id represents the name of the company or organization which has produced the artifact (e.g. org.apache.logging.log4j). Artifact-id is a unique string for the artifact (e.g. log4j) and version string (e.g. 1.2.17). When you specify your project&#8217;s dependencies, you have to write their Group-id, artifact-id and version.

You can also tell Maven to produce an artifact when compiling your project. The result will be a .jar file including metadata about the artifact. You can then install this jar file into a Maven repository and use it as a dependency in other projects.

## Build lifecycle

A build lifecyle is a set of steps (phases) which Maven does to build the project. You can define your own lifecycle but the default lifecycle is used most of the time. Here is a list of most important phases in the default lifecycle:

  1. **validate**: Make sure project structure is correct and all required data are available
  2. **compile**: Compile source code files
  3. **test**: Run unit test files
  4. **package**: Package compiled files in a distributable format (e.g. JAR)
  5. **install**: Install package into the local repository.

There is also another important lifecycle named &#8220;`clean`&#8220;. This lifecycle will clean-up artifacts created during build lifecycle.

You can call Maven command like tool and give the name of the phase or lifecycle to execute:

<pre>mvn clean package</pre>

Above command will run `clean` lifecycle and then will run build lifecycle up to &#8216;`package`&#8216; step. The target of the operation is the current directory.

## Directory structure

Maven expects a [standard project structure](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html) in your project. You have to follow this structure:

Suppose the root directory of the project is named &#8216;`myapp`&#8216;:

  * `myapp/pom.xml`
  * `myapp/src/main/java/com/mahdix/App.java`: Source code files should reside in this location and below, according to your package names.
  * `myapp/src/test/java/com/mahdix/Test.java`: Location for test files

You will run Maven command line utility on the root directory of the project where there is `pom.xml` file.

## POM.xml

Here is the general structure of a `pom.xml` file:

<pre>&lt;project xmlns="http://maven.apache.org/POM/4.0.0"&gt;
 &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
 
 &lt;groupId&gt;com.mahdix&lt;/groupId&gt;
 &lt;artifactId&gt;my-app&lt;/artifactId&gt;
 &lt;version&gt;1.0&lt;/version&gt;
 &lt;packaging&gt;jar&lt;/packaging&gt;
 
 &lt;name&gt;Test Application&lt;/name&gt;
 &lt;url&gt;http://www.mahdix.com&lt;/url&gt;
 
 &lt;dependencies&gt;
   &lt;dependency&gt;
     &lt;groupId&gt;junit&lt;/groupId&gt;
     &lt;artifactId&gt;junit&lt;/artifactId&gt;
     &lt;version&gt;4.8.2&lt;/version&gt;
   &lt;/dependency&gt;
&lt;/dependencies&gt;
&lt;/project&gt;</pre>

Here is explanation of important parts:

  * `modelVersion`: Specifies the model version of the Maven tool we use, this should always be 4.0.0
  * `groupId, artifactId, version`: These tags specify information about the current project
  * `dependencies`: This tag lists dependencies of the project. Maven will automatically fetch, download and install required JAR files according to the dependency list provided. You just need to specify artifacts that you need.

&nbsp;