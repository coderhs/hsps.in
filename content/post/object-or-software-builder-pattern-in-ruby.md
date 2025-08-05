---
title: "🏗️ Understanding the Builder Pattern with Ruby Examples"
date: 2025-08-05T10:57:22+05:30
lastmod: 2025-08-05T10:57:22+05:30
draft: false
keywords: ["builder", "software", "development", "pattern", "ruby", "api", "html", "json"]
description: "Implementation and example of Builder pattern in ruby"
tags: ["builder", "software", "development", "pattern", "ruby", "api", "html", "json"]
categories: ["Software Design Pattern"]
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

Design patterns are only useful when they help solve real-world problems in ways that feel natural in your language. In Ruby, one such pattern that fits like a glove is the **Builder Pattern**.

You’ll find it everywhere — from constructing HTML forms in Rails to building command-line interfaces or even assembling HTTP requests.


## 📦 What Is the Builder Pattern?

In real-world projects, we often model things as objects. The idea is that objects are instances of a common blueprint, sharing the same structure but differing in a few properties—like name, age, etc. However, when there are many properties to configure and you need more control over how the final object is built, that’s where the Builder pattern becomes useful. It helps construct complex objects step-by-step without cluttering your code with long initializers or deeply nested logic.

The **Builder Pattern** is used to construct complex objects step-by-step. Rather than stuffing all parameters into a huge constructor, you build an object one piece at a time, usually using a **fluent interface** (method chaining).

<!--more-->

## 🚗 Classic Example: Build a Car

```ruby
class Car
  attr_accessor :engine, :wheels, :color

  def to_s
    "Car with #{engine}, #{wheels} wheels, and color #{color}"
  end
end

class CarBuilder
  def initialize
    @car = Car.new
  end

  def set_engine(engine)
    @car.engine = engine
    self
  end

  def set_wheels(wheels)
    @car.wheels = wheels
    self
  end

  def set_color(color)
    @car.color = color
    self
  end

  def build
    @car
  end
end

car = CarBuilder.new
             .set_engine("V8")
             .set_wheels(4)
             .set_color("Red")
             .build

puts car
#=> Car with V8, 4 wheels, and color Red
```

---

## 🧾 Real-World DSL: Rails `form_with`

Now let’s look at a familiar example if you’ve worked with Rails:

```ruby
form_with(model: @post) do |form|
  form.text_field :title
  form.submit
end
```

Looks simple, right? But it’s doing exactly what the Builder pattern is all about:

  * You're passing a model (@post)
  * You yield a builder object (form)
  * You call form.text_field, form.submit, etc.
  * And under the hood, it builds up the full HTML form

Let’s try recreating a simplified version of form_with ourselves.

### Building a Minimal form_with DSL in Ruby

#### Step 1: A basic model

```rb
class Post
  attr_accessor :title

  def initialize(title:)
    @title = title
  end
end
```

#### Step 2: The FormBuilder


```rb
class FormBuilder
  def initialize(model)
    @model = model
    @output = ""
  end

  def text_field(field_name)
    model_name = @model.class.name.downcase
    value = @model.send(field_name)
    @output << %(<input type="text" name="#{model_name}[#{field_name}]" value="#{value}">\n)
  end

  def submit(value = "Submit")
    @output << %(<input type="submit" value="#{value}">\n)
  end

  def to_s
    @output
  end
end
```

#### Step 3: The form_with method

```rb
def form_with(model:)
  model_name = model.class.name.downcase
  puts %(<form action="/#{model_name}s" method="post">)

  builder = FormBuilder.new(model)
  yield(builder)

  puts builder.to_s
  puts %(</form>)
end
```

#### Step 4: Putting it all together

```rb
post = Post.new(title: "Hello World")

form_with(model: post) do |form|
  form.text_field :title
  form.submit "Save"
end
```


```ruby
<form action="/posts" method="post">
<input type="text" name="post[title]" value="Hello World">
<input type="submit" value="Save">
</form>
```

Here, `form_with` yields a builder object (`form`), which provides methods like `text_field`, `text_area`, and `submit`. You’re configuring it, and it produces HTML for you. This is a perfect example of the builder pattern in action.

Now let’s take the same principle and apply it elsewhere.

---

## 🔧 CLI Builder

```ruby
class FFmpegCommand
  def initialize
    @parts = ["ffmpeg"]
  end

  def input(file)
    @parts << "-i #{Shellwords.escape(file)}"
    self
  end

  def resolution(width:, height:)
    @parts << "-vf scale=#{width}:#{height}"
    self
  end

  def output(file)
    @parts << Shellwords.escape(file)
    self
  end

  def to_s
    @parts.join(" ")
  end
end

cmd = FFmpegCommand.new
          .input("video.mp4")
          .resolution(width: 1280, height: 720)
          .output("out.mp4")

puts cmd.to_s
#=> ffmpeg -i video.mp4 -vf scale=1280:720 out.mp4
```

---

## 🧱 JSON Builder

```ruby
require "json"

class JSONBuilder
  def initialize
    @data = {}
  end

  def set(key, value)
    @data[key] = value
    self
  end

  def nest(key)
    nested = JSONBuilder.new
    yield(nested)
    @data[key] = nested.data
    self
  end

  def build
    JSON.pretty_generate(@data)
  end

  protected

  def data
    @data
  end
end

builder = JSONBuilder.new
builder.set("name", "Harris")
       .set("age", 30)
       .nest("address") do |a|
         a.set("city", "Bangalore").set("zip", "560001")
       end

puts builder.build
```

### Output:

```json
{
  "name": "Harris",
  "age": 30,
  "address": {
    "city": "Bangalore",
    "zip": "560001"
  }
}
```

---

## 🌐 API Request Builder

```ruby
class APIRequestBuilder
  def initialize
    @headers = {}
    @query = {}
    @body = nil
  end

  def set_header(key, value)
    @headers[key] = value
    self
  end

  def set_query(key, value)
    @query[key] = value
    self
  end

  def set_body(data)
    @body = data
    self
  end

  def build
    {
      headers: @headers,
      query: @query,
      body: @body
    }
  end
end

request = APIRequestBuilder.new
           .set_header("Authorization", "Bearer token")
           .set_query("page", 2)
           .set_body({ title: "Builder Pattern" })

puts request.build
```

---

## 🧠 Takeaways

Builder-style DSLs are everywhere in Ruby:
- `ActiveRecord.where(...).order(...).limit(...)`
- `Mail.new(...)`
- `Nokogiri::XML::Builder`
- `Prawn::Document.generate(...)`
- Custom DSLs like the ones above

The core principle is:
> Let the user configure things step-by-step without forcing them to know or pass everything upfront.

---

## 💬 Want to Build Your Own?

- Think about the final code you want to *write* first.
- Yield objects with fluent methods (`self` at the end of each).
- Compose a final structure (`to_s`, `build`, or `to_json`).
- Avoid too much meta programming. Clean and simple Ruby is enough.

You don’t need Rails or fancy tooling to write clean DSLs — just plain Ruby and a clear idea of what you want to express.

## Important notes / Don't over Engineer

The Builder Pattern is useful, but it’s also easy to misuse or over-engineer. Here are some common mistakes people make, and what to avoid

1. Using Builder When It's Not Needed

  Don't use builder pattern for simple cases, use it only when.
  * The object has many optional fields
  * The creation process involved multiple steps
  * You want to make the code more readable

2. Mixing building logic and execution logic.

Use the builder to only build out the object/command/request. The execution should be done by afterwords allowing for
more maintainable and testable solution.

## Conclusion

The builder pattern is one of those quiet heroes in software design. Whether you're building cars, HTML forms in Rails, JSON payloads, API requests, or CLI commands—anywhere you need to construct something complex step by step—it gives you a clean, readable way to do it. You've probably used it without realizing it (like form_with in Rails), and once you spot the pattern, you’ll start seeing it everywhere.

It’s not just about cleaner code—it’s about giving your future self (and teammates) a better way to read, maintain, and extend that code without headaches.

So next time you find yourself passing 12 arguments into a constructor or setting a bunch of properties in random order, take a step back and ask: would a builder make this easier? Odds are, it will.

