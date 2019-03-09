---
layout: post
title: Up and running!
---

This is the first post!


```java
public abstract class CacheKey implements IMeasurableMemory
{
    public final TableId tableId;
    public final String indexName;

    protected CacheKey(TableId tableId, String indexName)
    {
        this.tableId = tableId;
        this.indexName = indexName;
    }
}
```
