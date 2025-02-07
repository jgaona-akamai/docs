---
title: Redis Mode Indexes
sidebar_position: 40
---

Indexes enable you to create searchable attributes to speed up your queries. For more information about indexes, refer to [Indexing](../indexing/index.md).

## Redis Mode Indexes

Redis Mode collections have several indexes created automatically.

The Redis API supports a fixed set of commands. Most of those operate on a single entry and don't need any special indexing (the purpose of indexes is basically to speed up operations that involve multiple entries). We have taken care to optimize the commands operating on multiple entries, so you should not need any additional indexes.

## View Indexes

View active indexes for a collection on the Indexes tab. The section explains what each element of the tab means.

![Redis Mode Indexes Tab](/img/collections/redis-mode-indexes.png)

- **ID -** This is a unique primary key for the indexes.
- **Type -** The index type.
- **Unique -** If `true`, then no two documents are allowed to have the same set of attribute values. Default is `true` for primary keys and indexes, and default is `false` is for all other keys and indexes.
- **Sparse -** If `true`, then a document is excluded from the index. If any index value is not set or has a null value, GDN does not perform uniqueness checks.
- **Deduplicate -** If `true` (default), GDN only inserts each non-unique index value once per document. Attempting to insert duplicate values results in an error. If `false`, GDN inserts each instance of the value into the index per document.
- **Extras -** Extra conditions of the index definition, such as minimum length for fulltext index.
- **Selectivity Est -** An estimate indicating the percentage of documents affected by the indexed attributes.
- **Fields -** The attributes on which the index is created.
- **Name -** A custom name generated for a non-primary index. The primary index is created and named during the creation of a collection.
- **Action -** Allows a user to delete or add indexes. The primary key is a unique identifier and cannot be deleted.
