---
title: 'Quickstart: distribute tables - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Quickstart to distribute table data across nodes in Azure Database for PostgreSQL - Hyperscale (Citus).
ms.author: jonels
author: jonels-msft
recommendations: false
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc, mode-ui
ms.topic: quickstart
ms.date: 05/05/2022
---

# Model and load data

[!INCLUDE[applies-to-postgresql-hyperscale](../includes/applies-to-postgresql-hyperscale.md)]

In this example, we'll use Hyperscale (Citus) to store and query events
recorded from GitHub open source contributors.

## Prerequisites

To follow this quickstart, you'll first need to:

1. [Create a server group](quickstart-create-portal.md) in the Azure portal.
2. [Connect to the server group](quickstart-connect-psql.md) with psql to
   run SQL commands.

## Create tables

Once you've connected via psql, let's create our table. Copy and paste the
following commands into the psql terminal window, and hit enter to run:

```sql
CREATE TABLE github_users
(
	user_id bigint,
	url text,
	login text,
	avatar_url text,
	gravatar_id text,
	display_login text
);

CREATE TABLE github_events
(
	event_id bigint,
	event_type text,
	event_public boolean,
	repo_id bigint,
	payload jsonb,
	repo jsonb,
	user_id bigint,
	org jsonb,
	created_at timestamp
);

CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Notice the GIN index on `payload` in `github_events`. The index allows fast
querying in the JSONB column. Since Citus is a PostgreSQL extension, Hyperscale
(Citus) supports advanced PostgreSQL features like the JSONB datatype for
storing semi-structured data.

## Distribute tables

`create_distributed_table()` is the magic function that Hyperscale (Citus)
provides to distribute tables and use resources across multiple machines.  The
function decomposes tables into shards, which can be spread across nodes for
increased storage and compute performance.

The server group in this quickstart uses the Basic Tier, so the shards will be
stored on just one node. However, if you later decide to graduate to the
Standard Tier, then the shards can be spread across more nodes. With Hyperscale
(Citus), you can start small and scale seamlessly.

Let's distribute the tables:

```sql
SELECT create_distributed_table('github_users', 'user_id');
SELECT create_distributed_table('github_events', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## Load data into distributed tables

We're ready to fill the tables with sample data. For this quickstart, we'll use
a dataset previously captured from the GitHub API.

Run the following commands to download example CSV files and load them into the
database tables. (The `curl` command downloads the files, and comes
pre-installed in the Azure Cloud Shell.)

```
-- download users and store in table

\COPY github_users FROM PROGRAM 'curl https://examples.citusdata.com/users.csv' WITH (FORMAT CSV)

-- download events and store in table

\COPY github_events FROM PROGRAM 'curl https://examples.citusdata.com/events.csv' WITH (FORMAT CSV)
```

We can review details of our distributed tables, including their sizes, with
the `citus_tables` view:

```sql
SELECT * FROM citus_tables;
```

```
  table_name   | citus_table_type | distribution_column | colocation_id | table_size | shard_count | table_owner | access_method 
---------------+------------------+---------------------+---------------+------------+-------------+-------------+---------------
 github_events | distributed      | user_id             |             1 | 388 MB     |          32 | citus       | heap
 github_users  | distributed      | user_id             |             1 | 39 MB      |          32 | citus       | heap
(2 rows)
```

## Next steps

Now we have distributed tables and loaded them with data. Next, let's try
running queries across the distributed tables.

> [!div class="nextstepaction"]
> [Run distributed queries >](quickstart-run-queries.md)
