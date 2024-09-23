---
title: "Using Docker Postgres to Take Sql Dump From AWS RDS"
date: 2024-09-23T04:38:52-07:00
lastmod: 2024-09-23T04:38:52-07:00
draft: false
keywords: ["aws", "rds", "postgres", "docker"]
description: "Using Docker Postgres to Take Sql Dump From AWS RDS"
tags: ["aws", "rds", "postgres", "docker"]
categories: ["docker", "postgres", "aws"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

Docker is an efficient way to use command-line tools without needing to install them or their required libraries. Recently, I had a use case where I needed to take an SQL dump of a schema from a PostgreSQL database. The problem was that the PostgreSQL DB was version 16.3, while my local DB was only version 15. Using my local `pg_dump` to access the remote database resulted in a version incompatibility error. To fix this, I would either need to upgrade `pg_dump` or have both versions installed simultaneously on my machine. I didn't want to do either. This is where Docker came to the rescue.

<!--more-->

The idea is to download the Docker image of the PostgreSQL version you need—in this case, `postgres:16.3`—and use the `pg_dump` tool from inside the container. Downloading the schema was just one part of the problem; the second part was uploading it to another database. In this article, I'm documenting the steps I followed to download the schema to the container and then upload it to the second database. I'll also include instructions for making the schema file available on the host machine with the proper permissions.

### Step 1:

Create a local folder to store the `pg_dumps`.

```sh
mkdir pg_dumps
```

### Step 2:

Launch the PostgreSQL container, mounting the local folder. This folder will be where the dump/SQL file is saved.

```sh
docker run --rm \
           --network="host" \
           --user $(id -u):$(id -g) \
           -v ./pg_dumps:/dump \
           -e PGPASSWORD='<PASSWORD>' \
           postgres:16.3 \
           bash -c "pg_dump -h <RDS_URL> -U <RDS_USER> -d <RDS_DB> -n schema_name > /dump/schema_name.sql"
```

If you remove the `-n schema_name`, it will dump the entire database.

Once the dump is complete, the container will exit.

### Step 3:

Import the schema to the new database.

```sh
docker run --rm \
           --network="host" \
           --user $(id -u):$(id -g) \
           -v ~/pg_dumps:/dump \
           -e PGPASSWORD='<your-password>' \
           postgres:latest \
           bash -c "pg_restore -h <2nd-db-rds-endpoint> -U <username> -d <target_database_name> < ./dump/schema_name.sql"
```

Once the import is complete, the container will exit.

And that’s it! You have successfully exported and imported a schema from one PostgreSQL server to another without needing to update or install parallel versions of PostgreSQL tools (`pg_dump` / `pg_restore`).
