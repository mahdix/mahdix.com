---
title: Benchmarking HTML parsers (Go vs Java)
permalink: /blog/2020/05/28/benchmarking_html_parsers_golang_vs_java/
categories:
  - Uncategorized
published: false
---

So, I am working on a web page extractor (something like Instapaper back-end) and I was wondering which language is a better fit for this type of code.

Most of my experience has been with Java, so I am pretty familiar with the language and IDEs and ..., 
but I have been working with Golang recently and it has been a positive experience. 

On one hand we have a very well optimized JVM with excellent GC and on the other hand we have Go with native executables. 
So, I wrote two sets of code to benchmark performance of each of these when they load and parse a webpage.

## Task

The task is to load a number of web pages and parse them as a DOM structure and then search for specific tags within the DOM. 
This is a simple example of what any web page extractor would need.

## Environment

In a benchmark, it is not important which hardware/OS environment you run the code. The only important factor is that they should be the same 
for all the different code you want to compare.

So I started a docker container on my Macbook with 4 CPUs and 8GB RAM. This is not an ideal RAM for high processing tasks like this but as I said, 
the point is just to "compare" the performances with each other and as long as they both run on the same environment we are fine.

## Frameworks

For Java I picked the well known [jsoup](https://jsoup.org/) library. This framework is actively maintained and has ~7900 stars on GitHub. 
It is well documented and easy to get started with.

For Go, I picked [goquery](https://github.com/PuerkitoBio/goquery). This one is also pretty active and has 8800 GitHub stars. 

## Benchmarking

To do the benchmark, I prepared a link file with a small number of links to some high traffic websites with lots of links and contents.
Below steps are done 10 times for each of Java and Go implementations:
1. Call parsing code with each link from the input file (HTML parsing only)
2. Call parsing code with each link from the input file + one of "a, div, span, p" selectors (HTML parse + document traverse)
3. Time each execution and log it to the output (language, url, selector, result, time taken).

## Source code

All of the source code (Go and Java implementations + scripts used to do the benchmarking) are available [here](...)

## Results

900 executions were made during the benchmark (450 for each language). 
- Average execution time for Golang across all 450 executions was 235 milliseconds.
- This number was 921 milliseconds for the Java implementation.

So, Go was about 4x faster than Java on average, which is impressive!







