---
title: "GIN: Generalized Inverted iNdex (on text)"
date: 2022-05-19T21:01:23-07:00
lastmod: 2022-05-19T21:01:23-07:00
draft: false
keywords: ["PostgreSQL", 'GIN']
description: "Intro to inverted index and GIN index"
tags: ["PostgreSQL", 'database']
categories: []
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

Indexes are an integral part of any production database. It improves the speed of your query, and which in tern speeds up your application. The faster you get your data, faster you can provide the result to your user, faster you can move on to your next user. Knowing different type of indexes allows you to choose which is the best for your data. As there is no silver bullet.

***PostgreSQL is the DB refereed to in this article***

This article is my humble attempt to explain and simplify what is Gin.

<!--more-->

Before explaining Inverted Indexes, a simple note on forward index.

## Forward Indexes

One of the common type of index we work with are called forward index. BTree is an example of forward index. In such index, you have an identifier pointing to the rest of the data. For example the primary key or id of your row. It points to which row the information you seek. Few examples of keys are user's email address, index with foreign key(s) or username. Basically you define and let the database know what exactly you want to index, and you know that this is the key you are looking for and using this key you can load the rest of the data with little or no overhead.

## Inverted Index

Inverted index is opposite of forwards index. To explain it simply we have a huge block of text or data, and we don't know what is or what to use as a key. A text, doc, html page, array or json have a lot of data in it. Some words in a text are important, some are not. Example words like the, a, is, and, etc doesn't make much sense to be indexed. As the chances for you search for that word is less, and even if you do you would rather want a web page or doc titled 'And' rather than all the web pages and docs in with the word 'and' in it.

Inverted index, parses and builds keys from the document and then create a list of documents that has this word in it.

Thus the term inverted or opposite of forward index. This type of index is expensive when it come to insert/update. As on each insert/update we might need to update several rows in the index based on the new set of words that was added/removed from the doc. But the speed improvement it provides based on the volume of data is remarkable. One thing that GIN (PostgreSQL) does is that it delays the update/insert of the index by adding the keywords to an unsorted list of pending entries. This is the sorted and entered when postgres does auto vacuum or when this list get too big (default is 4 MB). The overhead of this approach is that when the regular index scan is done, the system will have to do some sequential scanning on this unsorted list.

Eg: A table with over 7 million records, with one text field (with a maximum of 100-200 letters).

Before Index:


![Before](/images/gin_index/before.png)

Execution time: 1201.1 ms

After Index:

![After](/images/gin_index/after.png)

Execution time: 3.6 ms

## How to use GIN index in postgresql

We need to enable the extension pg_trgm, this is not directly related to gin but this has some useful functions such as an english dictionary which GIN can use to classify words in a document.

```sql
create extension pg_trgm;
```

```sql
CREATE INDEX users_activity_idx
   ON users USING gin (activity gin_trgm_ops);
```

If you want to use this in rails you can define the migration like below.

```rb
def change
    enable_extension("pg_trgm");
    add_index(:users, :user_activity, using: 'gin', opclass: :gin_trgm_ops)
end
```

## Issues with this index

Issues that i have come across on my research.

* Since the sorting or cleaning of the unsorted list is executed during auto vacuum, it would increase the duration of the auto vacuum.

