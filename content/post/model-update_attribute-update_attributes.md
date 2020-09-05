---
title: "Model Update_attribute Update_attributes"
date: 2013-01-23
lastmod: 2013-01-23
draft: false
keywords: []
description: ""
tags: []
categories: ['Rails']
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
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

<!--more-->
Lot of people might have a confusing between the functionality of update_attribute and update_attributes. The purpose of these functions can be understood from their name itself. the first one update_attribute would update a single attribute of the model

Its used as

```rb
@users = User.find(1)
@users.update_attribute(:attribute_name,"attribute_value")
```

and the later update_attributes updates a whole set of values in the model

```rb
@users = User.find(1)
@users.update_attributes(attribute_1:"attribute_1_value", attribute_2:"attribute_2_value")
```

Now the important differentiator of these two functions is that the first function update_attribute updates an attribute without running any of the validations mentioned..

So keep that in mind when you are using the update_attribute function, else at times you will find values you didn’t expect to be entered into the database found in them.