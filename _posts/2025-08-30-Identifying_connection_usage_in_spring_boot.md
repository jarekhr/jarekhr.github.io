---
title: How to identify code which holds DB connections for long?
date: 2025-08-30 17:12:15 +0200
categories: [Software, Performance]
tags: [programming, java, reliability, performance, jdbc, hikari, spring boot, observability]     # TAG names should always be lowercase
comments: on
---

# How to identify code which holds DB connections for long?

Recently we had a case when DB connection pool in our Spring Boot app was exhausted and coming requests were queueing. This caused observed response times from our endpoints to be very high - not good.
We wanted to figure out which pieces of code in the app were responsible for holding connections for long. We were quite confident that we don't have a 
traditional connection leak situation, but most likely some long-running logic was holding connections for much longer than anticipated.

## Usual suspects 

We had some usual suspects in mind:
 * long-running queries 
 * long-running transactions doing some non-DB related IO (reading files, etc.)

Each of the suspected scenarios could be happening as a result of many different operations, so instead of guessing better to check what is 
actually happening in the app.

## Spring Boot and Hikari CP

By default, Spring Boot ships Hikari connection pool lib. There's one cool property there: 

```yaml
spring.datasource.hikari.leak-detection-threshold=3000
```

The above property set in Spring Boot will turn-on leak connection detection in Hikari CP. By default, value of this property is set to `0`, which means it is
switched off. Value represents number of milliseconds after which DB connection "leased" from the pool is considered as potentially leaked. In the example above, if connection
is not returned after 2000ms, Hikari will consider it a potential connection leak and report it.

## How does that work

Normal flow for the connection handling when your code is using transactions is as follows:
1. Transaction starts, **DB connection is acquired**. There's a call to connection pool to get connection. At this point Hikari **starts measuring time** and records the stack trace - to help with identifying piece of code which asked for connection
2. Your normal logic runs, there's potentially multiple DB queries run, all using same DB connection
3. Your code commits transaction, connection is **returned to the pool**. Hikari stops measuring time.

When the `leak-detection-threshold` property is set, Hikari is using extra thread to monitor leased connections for how much time has passed since connection was leased. If measured 
time is above the threshold, it will print out `WARN` messages like the one below:

```
2025-08-30T17:18:01.034+03:00  WARN 12345 --- [Housekeeper] com.zaxxer.hikari.pool.ProxyLeakTask     : Connection leak detection triggered for org.postgresql.jdbc.PgConnection@aaaaaa on thread http-nio-11080-exec-9, stack trace follows
java.lang.Exception: Apparent connection leak detected
 at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:128) ~[HikariCP-5.0.1.jar:na]
 at org.springframework.jdbc.datasource.DataSourceUtils.fetchConnection
 at some.of.your.code.here
```

> Analyze stack trace printed by Hikari to figure out which part of your code requested the connection.
{: .prompt-tip }

## Summary

In our case we found out that our queries were running fast, however we had a process which was running within the transaction while
doing lots of non-DB I/O - reading relatively big files over network. Because this reading was happening within transaction context, DB connection was still being allocated and not returned to the pool. 
Unfortunately, it was synchronous process triggered by REST call handling. Which means, there could be many clients initiating this process at the same time.
If this happened, we would have lots of connections leased and pool was getting exhausted quickly. Server was not able to handle requests which were requiring DB connections - all such connections would be "hanging" on attempt to get connection from `DataSource`

Turning on `leak-detection-threshold` allowed us to quickly pinpoint problematic part in the code and refactor it to be more efficient.
  * set `leak-detection-threshold` to some reasonable value. Setting it to `2000ms` seems to be a good idea (depending on the app)
  * if you want to ensure that some of your code is never used in transaction context, annotate that piece of code with `@Transactional(propagation = Propagation.NEVER)`. If such
    method is called with running transaction, an exception will be thrown - and you would know which part of code needs fixing.

```java
  @Transactional(propagation = Propagation.NEVER)
  public void doFileProcessing(String filename) {
      // lots of IO which takes time
  }
  
```

