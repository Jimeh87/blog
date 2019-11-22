---
layout: post
title: Truncating every table in a schema with HSQLDB
subtitle: Great for unit tests
image: /img/hsqldb.jpg
bigimg: /img/datatube.jpg
---

[HSQLDB](http://hsqldb.org/) is a great in memory database for Java. It comes in really handy when writing integration tests. 
Here is how you truncate all of the tables in a schema. Great for keeping data from leaking between unit tests.

## How to do it

You just need to run the following HSQLDB statement to delete all that pesky table data.

```sql
TRUNCATE SCHEMA PUBLIC AND COMMIT NO CHECK;
```

Where `PUBLIC` is the schema name.

## When to use it

I typically use it as a tear down script between tests or test classes depending on the database size.

Hope this helps!

