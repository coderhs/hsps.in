---
title: "Validating YAML Files in Ruby"
date: 2022-08-22T21:27:34-07:00
lastmod: 2022-08-22T21:27:34-07:00
draft: false
keywords: ["yaml", "dry-schema", "dry-validation", "yaml", "toml", "schema", "structure", "ruby", "software engineering", "best practice"]
description: "How to validate YAML configuration files in Ruby using dry-schema and dry-validation gems with practical examples."
tags: ["yaml", "dry-schema", "dry-validation", "yaml", "toml", "schema", "structure", "ruby", "software engineering", "best practice"]
categories: ["ruby", "yaml", "gems"]
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

YAML stands for "YAML Ain't Markup Language". YAML is a data serialization language that is often used for writing configuration files.

In simple terms YAML is like XML or JSON, but for human beings rather than computers. I feel this is getting overlooked a lot, programmers need to understand that. Another human like configuration language I like is TOML (Tom's Obvious Minimal Language).

In a static language (like crystal) you need to define the structure of a YAML or JSON file. Defining the structure also act as validation of its schema. Validating the schema is a good idea because it ensure's that all the required data is present. Its better to know data is missing when your program starts rather than while it is running.

For dynamic language like ruby you can load a yaml file, and ruby will dynamically initialize it. It does make your life easier as a programmer. You don't need to define the schema, and remember to update the schema when you add a new variable to your file.

But defining the schema has its benefits -
1. For the User: It reminds then to have the configuration filled before running their program
2. For the Developer: It helps us to eliminate the configuration file as a possible reason for the program not running.

<!--more-->

The library that I use is [`dry-schema`](https://github.com/dry-rb/dry-schema). I felt it to be robust, expressive and extensible

`dry-schema` can is used to confirm schema of json files, yamls files, toml files, form parameters, hash, etc.

 If you want more extensive business logic validation you can use `dry-validation`. In this article I will explain how to use them both with an example from a recent project.

I created a web scrapper to copy articles (my articles) from an old blog (html files) to markdown files. Even though it was being written for personal use, I wrote it such a way that I can reuse it for future by me or anyone else. This was a command line application, that allow you to set custom scrapping rule for each domain.

I created a `config.yml` file, for the user to define the domain and the custom rule class. The current contents for the file are as follows

```yaml
domains:
	- domain: https://domain1.com/blog/author/coderhs
	  rule: DomainOne
	- domain: https://Domain2.in
	  rule: DomainTwo
```

Its an array of hash with `domain` key for the root url and `rule` key defining the class name with the code for scrapping. You can add more domains in future and add custom classes and place them inside the rules folder.

Now to confirm yaml file, we start by adding `gem 'dry-schema'` to the `Gemfile` (followed by `bundle install`). Then add the following to `main.rb` file (this is file that starts my program):

```rb
require 'yaml'
require 'dry/schema'

config = YAML.load(File.open("config.yml"))
# => {domains: [{domain: "https://domain1.com/blog/author/coderhs", rule: "DomainOne"},..]}

config_schema = Dry::Schema.Params do
  required(:domains).value(:array, min_size?: 1).each do
    hash do
      required(:domain).filled(:string)
      required(:rule).filled(:string)
    end
  end
end

schema_validation = config_schema.call(config)

raise schema_validate.errors.to_h.to_s unless schema_validation.success?

```

Now if the required format (the keys) is not present in our yaml file, this will raise an error.

```sh
main.rb:17:in `<main>': {:domains=>{0=>{:rule=>["is missing"]}}} (RuntimeError)
```

Now we can take another step further and validate that the domain is filled with a url. For that we will use another gem called `dry-validation`. `dry-validation`, also does schema validations using `dry-schema` gem.

We start by creating a new ruby file `config_validation.rb` and inside which we write the following code

```rb
class ConfigValidation < Dry::Validation::Contract
  params do
		required(:domains).value(:array, min_size?: 1).each do
      hash do
        required(:domain).filled(:string)
        required(:rule).filled(:string)
      end
    end
  end

  rule(:domains).each do |index:|
    unless /^https?:\/\/(?:www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b(?:[-a-zA-Z0-9()@:%_\+.~#?&\/=]*)$/.match?(value[:domain])
      key([:domains, :domain, index]).failure('has invalid format')
    end
  end
end
```

and in the `main.rb`

```rb
require 'yaml'
require 'dry/schema'
require 'dry/validation'
require_relative "./config_validation"

config = YAML.load(File.open("config.yml"))
# => {domains: [{domain: "https://domain1.com/blog/author/coderhs", rule: "DomainOne"},..]}

validation = ConfigValidation.new
validation_result = validation.call(config)

raise validation_result.errors.to_h.to_s unless validation_result.success?

```

How the error will look like if you pass in invalid URL:

```sh
main.rb:17:in `<main>': {:domains=>{:domain=>{0=>["has invalid format"], 1=>["has invalid format"]}}} (RuntimeError)
```

This article is meant to be an intro, you can find more details from the gem's documentation.

Note:

* If you have extra keys the system will let it through (need to confirm if there is any method to prevent that)
* You can validate any file or format as long as you can pass it to the call method as a hash or keyword arguments


Reference Links:

* [https://dry-rb.org/gems/dry-schema/](https://dry-rb.org/gems/dry-schema/)
* [https://dry-rb.org/gems/dry-validation/1.8/](https://dry-rb.org/gems/dry-validation/1.8/)
* [https://yaml.org/](https://yaml.org/)
* [https://toml.io/en/](https://toml.io/en/)
