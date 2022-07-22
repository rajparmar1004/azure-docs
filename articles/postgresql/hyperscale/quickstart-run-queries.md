---
title: 'Quickstart: Run queries - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Quickstart to run queries on table data in Azure Database for PostgreSQL - Hyperscale (Citus).
ms.author: jonels
author: jonels-msft
recommendations: false
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc, mode-ui
ms.topic: quickstart
ms.date: 05/05/2022
---

# Run queries

[!INCLUDE[applies-to-postgresql-hyperscale](../includes/applies-to-postgresql-hyperscale.md)]

## Prerequisites

To follow this quickstart, you'll first need to:

1. [Create a server group](quickstart-create-portal.md) in the Azure portal.
2. [Connect to the server group](quickstart-connect-psql.md) with psql to
   run SQL commands.
3. [Create and distribute tables](quickstart-distribute-tables.md) with our
   example dataset.

## Distributed queries

Now it's time for the fun part in our quickstart series--running queries.
Let's start with a simple `count (*)` to verify how much data we loaded in
the previous section.

```sql
-- count all rows (across shards)

SELECT count(*) FROM github_users;
```

```
 count
--------
 264308
(1 row)
```

Recall that `github_users` is a distributed table, meaning its data is divided
between multiple shards. Hyperscale (Citus) automatically runs the count on all
shards in parallel, and combines the results.

Let's continue looking at a few more query examples:

```sql
-- Find all events for a single user.
-- (A common transactional/operational query)

SELECT created_at, event_type, repo->>'name' AS repo_name
  FROM github_events
 WHERE user_id = 3861633;
```

```
     created_at      |  event_type  |              repo_name
---------------------+--------------+--------------------------------------
 2016-12-01 06:28:44 | PushEvent    | sczhengyabin/Google-Image-Downloader
 2016-12-01 06:29:27 | CreateEvent  | sczhengyabin/Google-Image-Downloader
 2016-12-01 06:36:47 | ReleaseEvent | sczhengyabin/Google-Image-Downloader
 2016-12-01 06:42:35 | WatchEvent   | sczhengyabin/Google-Image-Downloader
 2016-12-01 07:45:58 | IssuesEvent  | sczhengyabin/Google-Image-Downloader
(5 rows)
```

## More complicated queries

Here's an example of a more complicated query, which retrieves hourly
statistics for push events on GitHub. It uses PostgreSQL's JSONB feature to
handle semi-structured data.

```sql
-- Querying JSONB type. Query is parallelized across nodes.
-- Find the number of commits on the master branch per hour 

SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM   github_events
WHERE  event_type = 'PushEvent' AND
       payload @> '{"ref":"refs/heads/master"}'
GROUP BY hour
ORDER BY hour;
```

```
        hour         | num_commits
---------------------+-------------
 2016-12-01 05:00:00 |       13051
 2016-12-01 06:00:00 |       43480
 2016-12-01 07:00:00 |       34254
 2016-12-01 08:00:00 |       29307
(4 rows)
```

Hyperscale (Citus) combines the power of SQL and NoSQL datastores
with structured and semi-structured data.

In addition to running queries, Hyperscale (Citus) also applies data definition
changes across the shards of a distributed table:

```sql
-- DDL commands that are also parallelized

ALTER TABLE github_users ADD COLUMN dummy_column integer;
```

## Next steps

The quickstart is now complete. You've successfully created a scalable
Hyperscale (Citus) server group, created tables, distributed them, loaded data,
and run distributed queries.

Now you're ready to learn to build applications with Hyperscale (Citus).

> [!div class="nextstepaction"]
> [Build scalable applications >](howto-build-scalable-apps-overview.md)

> [!div class="nextstepaction"]
> [App stacks >](howto-app-stacks-overview.md)
