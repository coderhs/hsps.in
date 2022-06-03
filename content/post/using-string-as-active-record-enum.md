---
title: "Using String in Active Record(Rails) Enum"
date: 2022-05-31T20:10:36-07:00
lastmod: 2022-05-31T20:10:36-07:00
draft: false
keywords: ["Rails", "ruby"]
description: ""
tags: ["ruby", "rails"]
categories: ["rails tips"]
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

Rails model helps you define enum on a database integer field quite easily. The enum method which rails provide in its model is not an interface to the enum datatype available in some databases, but converting or using a integer field as an enum. Enum type or Enumerated type is user defined data type where you define the set of value the data type will have. If you consider regular or basic or primitive data types, they are pretty much like enum where the values it can hold is defined already by the language. Ruby dynamic type system does hide all this complexity for us while we develop.

Enum is quite useful when you want to make sure that only a specified list of values are saved for a field.

<!--more-->

Now the way active model enum works is tat you define an array of statuses, and then rails would save there indexes in the database. For example:

pre rails 7 format

```rb
class Article < ActiveModel
  enum status: [:draft, :published]
end
```

rails 7 format

```rb
class Article < ActiveModel
  enum :status, [:draft, :published]
end
```

The status field is usually an integer field as shown the in rails documentation[https://api.rubyonrails.org/v5.2.4.4/classes/ActiveRecord/Enum.html]. The advantage of this approach is that it automatically creates a lot of magic functions (rails magic) like `article.draft?`, `article.published?`, and even make the indexes of these accessible through the method `Article.statuses[:draft]`.

Now I haven't personally been a fan of this, a preferred to define my statuses as constants and a string.
eg:

`DRAFT = 'draft'.freeze` or `DRAFT = 0`. Its just something i was used to since I started working in rails and its much more readable when i write raw queries (in my opinion). But the thing about working with teams is that you have to make sacrifices and keep with the most common known approach as much as possible so that developer of any level who read your code can understand. But, even if i do agree with using enum data type, its hard for us to use integer because we work with databases a lot. We have our analyst and support team checking the values of various fields in our database and it would be more easier for them to read the term 'draft' rather than the value 0.

Thus we come to the reason for this article, explaining and demonstrating how we can use string for enum in rails.

The enum method in active record(rails) is actually expecting you to enter the field name and a hash with the mapping of various terms and its index values. When we enter an array of statuses, the method converts it to hash and then use it.

Thus both this

```rb
class Article < ActiveModel
  enum :status, [:draft, :published]
end
```

and this

```rb
class Article < ActiveModel
  enum :status, { draft: 0, published: 1 }
end
```

are valid.

So to use string in the database we just need to supply a has with string values, or convert an array to hash with itself.

```rb
class Article < ActiveModel
  DRAFT     = 'draft'.freeze
  PUBLISHED = 'published'.freeze
  STATUSES  = [DRAFT. PUBLISHED]
  enum status: STATUSES.zip(STATUSES).to_h
end
```

The above code is equivalent to writing like below.

```rb
class Article < ActiveModel
  DRAFT     = 'draft'.freeze
  PUBLISHED = 'published'.freeze
  STATUSES  = [DRAFT. PUBLISHED]
  enum status: {
    draft: DRAFT,
    published: PUBLISHED
  }
end
```

In rails 7.0 its much more cleaner to use string.

```rb
class Article < ActiveModel
  enum :status, draft: 'draft', published: 'published'
end
```

They do say that it will lead to slower query, but just make sure that the field is indexed and we are good.

Using string will make your code more readable.

Before you had to do

```rb
Article.where(status: Article.statuses(:draft))
# agree you do get the magic scop Article.drafts
```

but now you can do a more natural approach of

```rb
Article.where(status: :draft)
# or
Article.where(status: 'draft')
# or like how i would like to do it
Article.where(status: Article::DRAFT)
```

Happy Coding! :)
