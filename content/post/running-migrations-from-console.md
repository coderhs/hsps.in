---
title: "Running Migrations From Console"
date: 2022-07-19T23:15:19-07:00
lastmod: 2022-07-19T23:15:19-07:00
draft: false
keywords: ["rails", "til", "database", "console", "irb"]
description: ""
tags: ["rails", "til", "database", "console", "irb"]
categories: ["rails", "til", "Apartment"]
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

Note: I am using Apartment gem to manage my multi schema database, and this article is written with expectation you know and use that gem.

When you have multiple schema in your rails application, it is important for them to remain consistent. Rails migration is run by keeping track of the timestamp prefixed in front of its file name, it store's the database. So when you restore a schema that hasn't ran the migration, but rest of the schema's has it rails thinks its already ran the migration. Rails look at the main / default schema to know if has ran the migrations then follow up on the rest.


<!--more-->

To run the migrations in that schema you just restored, log on to the rails console. `rails c`, switch to the restored schema (`Apartment::Tenant.switch!('restored_schema')`) and run the following code.

```rb
ActiveRecord::MigrationContext.new("db/migrate").migrate
```

to rollback:

```rb
ActiveRecord::MigrationContext.new("db/migrate").rollback 1
```
