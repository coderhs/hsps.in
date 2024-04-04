---
title: "Create JSON Payload for API Test Using Factory Bot"
date: 2023-08-05T23:06:27-07:00
lastmod: 2023-08-05T23:06:27-07:00
draft: false
keywords: ["rails", "tdd", "testing", "factory-bot", "mini-test", "rspec"]
description: "Create JSON Payload for API Test Using Factory Bot"
tags: ["rails", "tdd", "testing", "factory-bot", "mini-test", "rspec"]
categories: ["rails", "TDD"]
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

I am not going to reiterate the PSA (Public Safety Announcement) of how important it is to write test. You can find enough articles related that online, and most probably you already know and agree to it. This article is how to generate JSON payload using Factory Bot.

[Factory Bot](https://github.com/thoughtbot/factory_bot) is a ruby library that is used to create records in the table for testing. It will help generate data to fill and prepare records, before the test execution.

<!--more-->

eg:

```rb
FactoryBot.define do
  factory :invoices, class: Invoice do
    sequence(:order_number) { |n| "Invoice_#{n}" }
    description { Faker::Lorem.sentence }
    customer_id { Faker::Alphanumeric.alpha(number: 7) }
    customer_name { Faker::Company.name }
    total_qty { 100 }
    total_cost { 100_000.0 }
  end
end

# Now if you want to create this record in your table.

FactoryBot.create(:invoices) # this is create a record in your DB.

FactoryBot.build(:invoices) # this would create an object of invoice, but not commit it to the DB.
```

The way factory bot works is that it generates the fields in the block and then assign them to the object of the class. The factory bot expects there are setters `x.field=` in the object. If there isn't one it will raise errors. So if our payload is similar or same as the rails model then you can write the code as below:

```rb
FactoryBot.build(:invoices).attributes.except('created_at', 'id', 'updated_at').as_json
```

Now if the field names of the payload has nothing to do with fields of the database. What you need to do is generate a Factory Bot against an object where you can set dynamic fields. A good candidate for that is `OpenStruct`. But when we convert an `OpenStruct` to json `OpenStruct.new.as_json` the JSON is as follows: `{"table"=>{}}`. To fix this, we create a new class inheriting the `OpenStruct`

```rb
class JSONStruct < OpenStruct
  def as_json(*args)
    super.as_json['table']
  end
end
```

In the object of the new child class, when you do `as_json`, it just returns the contents of the `table` key.

Now with the new class you can build a factory against it.

```rb
FactoryBot.define do
  factory :address_payload, class: JSONStruct do
    name { Faker::Company.name }
    address1 { Faker::Address.street_address }
    address2 { Faker::Address.secondary_address }
    city { Faker::Address.city }
    state { Faker::Address.state }
    zip { Faker::Address.zip }
  end
end

FactoryBot.define do
  factory :invoice_payload, class: JSONStruct do
    sequence(:order_number) { |n| "Invoice_#{n}" }
    description { Faker::Lorem.sentence }
    customer_id { Faker::Alphanumeric.alpha(number: 7) }
    customer_name { Faker::Company.name }
    total_qty { 100 }
    total_cost { 100_000.0 }
    after(:build) do |invoice|
      invoice.billing_address = FactoryBot.create(:address_payload, name: invoice.customer_name)
      invoice.shipping_address = FactoryBot.create(:address_payload, name: invoice.customer_name)
    end
  end
end

# Now if your run

FactoryBot.create(:invoice_payload).as_json
```

Output:

```json
{"order_number"=>"Invoice_1",
 "description"=>"Eius quia consequatur omnis.",
 "customer_id"=>"crvxboz",
 "customer_name"=>"Kirlin-Reynolds",
 "total_qty"=>100,
 "total_cost"=>100000.0,
 "billing_address"=>
  {"name"=>"Kirlin-Reynolds",
   "address1"=>"4459 Towne Land",
   "address2"=>"Suite 326",
   "city"=>"Lake Jeffreychester",
   "state"=>"Utah",
   "zip"=>"54710"},
 "shipping_address"=>
  {"name"=>"Kirlin-Reynolds",
   "address1"=>"973 Nia Alley",
   "address2"=>"Suite 824",
   "city"=>"Alexandriaview",
   "state"=>"Wyoming",
   "zip"=>"96348-5106"}}
```

This method allows you to easily create API Payload using Factory Bot, and share/use it across your test suit.

```rb
describe '#create' do
  it 'should create an invoice' do
    params = FactoryBot.create(:invoice_payload)
    post invoices_api_path(key: 'someramdomkey'), params: params.as_json

    assert_equal 1, Invoice.count
    assert_equal params.order_number, Invoice.first.order_number
  end
end
```

Happy Testing
