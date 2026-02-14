---
title: "Setup Read Replica (master-slave) Database on Postgresql 17"
date: 2024-10-24T10:19:48-07:00
lastmod: 2024-10-24T10:19:48-07:00
draft: false
keywords: ["postgresql", "master-slave", "database", "read-only", "replication", "node"]
description: "Setup read-replica for your postgresql 17 Database"
tags: ["project-pg_spider", "performance", "database", "postgresql"]
categories: ["postgresql"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
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

PostgreSQL is a mature, open-source relational database that has been widely adopted by many companies over the years. It has been my go-to database for most of my software career, helping me solve various performance challenges in my projects. Features like JSON generation, materialized views, and others have enabled me to scale APIs to handle millions of requests per second.

One effective strategy to improve the performance of web applications is to create a read-only replica of the primary database. This allows you to direct read-only queries (e.g., `SELECT` queries) to the replica, while write operations continue to target the primary database. Note that PostgreSQL doesn’t support multiple primary (or master) databases natively, but it does support multiple read replicas.

Although you can achieve distributed primary databases via schema or table sharding, that topic is beyond the scope of this article.

<!--more-->

If you are using a managed PostgreSQL service like Amazon RDS or DigitalOcean databases, creating a read replica is often as simple as clicking a button. However, this guide is for Linux administrators or PostgreSQL enthusiasts who want to manually set up read replicas.

## Setting Up PostgreSQL on Debian/Ubuntu

Since I primarily use Debian/Ubuntu, this guide will focus on these distributions. However, the steps related to modifying PostgreSQL files are similar across other Linux distributions.

### Adding the PostgreSQL Repository

First, ensure you have the latest PostgreSQL repository:

```sh
sudo apt update
sudo apt install -y wget gnupg lsb-release
wget -qO - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
echo "deb http://apt.postgresql.org/pub/repos/apt/ \$(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
sudo apt update
```

### Installing PostgreSQL

```sh
sudo apt install -y postgresql-17
```

### Enabling and Starting PostgreSQL

```sh
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

### Confirming the Installed Version

```sh
psql --version
```

You should see: `psql (PostgreSQL) 17.0`.

Make sure that the secondary (replica) server has the same version of PostgreSQL installed. The primary and replica servers need to have identical software configurations, though you can opt for lower CPU and RAM on the replica based on your workload.

## Configuring Primary and Replica Servers

Let’s assume the IPs are:

- **Primary:** 192.168.1.101
- **Replica:** 192.168.1.100

### On the Primary Server

1. **Create a Replication User:**

   Switch to the PostgreSQL user and create a user for replication:

   ```sh
   sudo su postgres
   psql
   ```

   Run:

   ```sql
   CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'replicaPassword';
   ```

2. **Allow Replica Server Access:**

   Add the replica server's IP to `pg_hba.conf`:

   ```sh
   echo "host replication replicator 192.168.1.100/32 md5" >> /etc/postgresql/17/main/pg_hba.conf
   ```

   Reload the configuration:

   ```sh
   psql -c "select pg_reload_conf();"
   ```

### On the Replica Server

1. **Verify Primary Server Connection:**

   Ensure you can connect to the primary server:

   ```sh
   telnet 192.168.1.101 5432
   ```

2. **Stop PostgreSQL:**

   ```sh
   sudo systemctl stop postgresql
   ```

3. **Backup Existing Data:**

   ```sh
   sudo su postgres
   cp -R /var/lib/postgresql/17/main/ /var/lib/postgresql/17/main_bak/
   rm -rf /var/lib/postgresql/17/main/
   ```

4. **Initialize the Replica with `pg_basebackup`:**

   ```sh
   pg_basebackup -h 192.168.1.101 -D /var/lib/postgresql/17/main/ -U replicator -P -v -R -X stream -C -S replicaslot1
   ```

5. **Confirm Replica Creation:**

   Check the presence of the `standby.signal` file:

   ```sh
   ls -ltrh /var/lib/postgresql/17/main/
   ```

## Testing Replication

Once the replica is set up, you can test it by creating databases and tables on the primary server and verifying their presence on the replica.

### Example:

#### On the Primary Database

```sql
CREATE DATABASE testing_db;
\c testing_db
CREATE TABLE users (id serial primary key, username text);
INSERT INTO users (username) VALUES ('Superman');
INSERT INTO users (username) VALUES ('Spiderman');
```

#### On the Replica Database

Switch to the replicated database and check if the table and data are present:

```sql
\c testing_db
SELECT * FROM users;
```
## Configuring Synchronous Commit (Optional)

By default, replication is asynchronous, meaning the primary does not wait for the replica to confirm data writes. To ensure data is written to the replica before confirming, enable synchronous commit:

```sql
ALTER SYSTEM SET synchronous_standby_names TO '*';
SELECT pg_reload_conf();
```

## Conclusion

By following these steps, you have successfully set up a read-only replica of your primary PostgreSQL database. This setup improves availability, data redundancy, and overall application performance by offloading read queries from the primary database. Implementing this architecture ensures that write operations are faster and read-heavy operations are handled efficiently.

Integrating read replicas can be straightforward in most frameworks. For example, in a Rails application:

```ruby
ActiveRecord::Base.connected_to(role: :reading) do
  # Read-only queries
  User.where(active: true).first
end

ActiveRecord::Base.connected_to(role: :writing) do
  # Write queries
  User.create(name: "John Doe")
end
```

A well-architected read replica setup can be transformative, enhancing your app's scalability and reliability. By distributing the workload efficiently, you can provide users with a more responsive experience and better overall performance.
