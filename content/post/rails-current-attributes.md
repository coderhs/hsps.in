---
title: "A Complete Guide to Rails.current_attributes (ActiveSupport::CurrentAttributes)"
date: 2025-07-08T18:31:49+05:30
lastmod: 2025-07-08T18:31:49+05:30
draft: false
keywords: ["rail", "current attributes", "thread safe", "scope"]
description: "Deep dive into Rails.current_attributes (ActiveSupport::CurrentAttributes)"
tags: ["rails", "thread"]
categories: ["rails"]
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

Managing context like the current user, store, client, or request ID across controllers, models, jobs, and services in a Rails app has always been a little messy. Before Rails 5.2, you might’ve reached for `Thread.current`, `class_attribute`, or even `@@class_variables` to store this kind of state. But these are not thread-safe and can cause hard-to-debug issues in concurrent environments.

Rails 5.2 introduced a clean solution: `ActiveSupport::CurrentAttributes`.

This article covers what it is, why it exists, how it works internally, how to use it safely across web and background jobs, and how to test it properly.

---

## 📜 A Quick History

`ActiveSupport::CurrentAttributes` was introduced by DHH in Rails 5.2 to simplify access to per-request or per-job context in a thread-safe way. It replaced older, hackier methods like:

```ruby
Thread.current[:current_user] = user
ApplicationRecord.class_attribute :current_client
```

These approaches were shared across threads — fine in development, dangerous in production.

`CurrentAttributes` changed that by giving you a dedicated, Rails-friendly API for storing context that’s isolated per request/job.

<!--more-->


---

## 🧩 What Is `CurrentAttributes`?

`CurrentAttributes` is a base class that lets you define request- or job-scoped attributes (like `user`, `client`, or `store`) and access them globally during execution.

```ruby
# app/models/current.rb
class Current < ActiveSupport::CurrentAttributes
  attribute :user, :client, :store

  def user=(user)
    super
    Time.zone = user.time_zone if user.respond_to?(:time_zone)
  end
end
```

Set it early in the request or job:

```ruby
class ApplicationController < ActionController::Base
  before_action :set_current_context

  def set_current_context
    Current.user = current_user
    Current.store = Store.find_by!(subdomain: request.subdomain)
    Current.client = request.headers["X-Client-ID"]
  end
end
```

Now in any service, model, or job:

```ruby
AuditLog.create!(user: Current.user, store: Current.store)
```

---

## 🚫 Why Not Use `class_attribute` or `Thread.current`?

Because:

- ❌ `class_attribute` and `@@variables` are **global across all threads**.
- ❌ `Thread.current` is unstructured and doesn’t reset automatically.
- ❌ These approaches are hard to test and can lead to subtle concurrency bugs.

---

## 🛠 How Does It Work Internally?

### Storage

Each attribute is stored using `Thread.current` or `Fiber.current` (depending on your Ruby version), isolated per thread or fiber.

```ruby
def current_attributes
  storage[self.name] ||= {}
end

def storage
  ActiveSupport::IsolatedExecutionState[:current_attributes] ||= {}
end
```

### Resetting

Rails automatically resets context after each request using `ActionDispatch::Executor`:

```ruby
Current.reset_all
```

You can (and should) call this manually at the end of background jobs.

---

## ✅ Using It in Sidekiq

```ruby
class MyJob
  include Sidekiq::Job

  def perform(client_id)
    Current.set(client: Client.find(client_id)) do
      ImportantService.call
    end
  end
end
```

This is thread-safe: each Sidekiq job runs in its own thread, and `CurrentAttributes` isolates state per job.

---

## ✅ Using It in Tests

### Manual setup:

```ruby
class OrdersControllerTest < ActionDispatch::IntegrationTest
  setup do
    @user = users(:alice)
    @store = stores(:default)

    Current.user = @user
    Current.store = @store
  end

  teardown do
    Current.reset_all
  end

  test "should create order" do
    post orders_url, params: { order: { name: "Test Order" } }
    assert_equal @user, Order.last.user
  end
end
```

### Shared helper:

```ruby
# test/test_helper.rb
module CurrentHelper
  def with_current(user: nil, store: nil, client: nil)
    Current.user = user if user
    Current.store = store if store
    Current.client = client if client
    yield
  ensure
    Current.reset_all
  end
end
```

Now in tests:

```ruby
test "with context" do
  with_current(user: @user, store: @store) do
    post orders_url, params: { order: { name: "Test" } }
  end
end
```

---

## ✅ Best Practices

| Do ✅                               | Don’t ❌                                 |
|------------------------------------|------------------------------------------|
| Set `Current.*` early              | Load values lazily from inside `Current` |
| Use `Current.set {}` blocks        | Store heavy or unrelated state           |
| Reset `Current` after each job/test| Use `class_attribute` for request context|

---

## 🧠 Summary

- `CurrentAttributes` is a clean, thread-safe, per-request container.
- Use it to store context like `user`, `store`, and `client`.
- Set it early in controllers or jobs.
- Avoid putting logic inside it.
- Use `reset_all` manually in jobs and tests.

It’s not just a substitute for `class_attribute` or `Thread.current` — it’s the right tool for request-scoped state in modern Rails apps.
