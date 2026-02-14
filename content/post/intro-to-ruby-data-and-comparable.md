---
title: "Ruby `Data` Class – A Convenient Way to Create Value Objects"
date: 2025-06-02T11:50:45+05:30
lastmod: 2025-06-02T11:50:45+05:30
draft: false
keywords: ["ruby", "core", "data", "comparable", "value objects", "immutable"]
description: "Ruby `Data` Class – A Convenient Way to Create Value Objects. An Introduction to Ruby Data class and comparable module of Ruby. "
tags: ["ruby", "core", "data", "comparable", "value objects", "immutable"]
categories: ["ruby"]
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

Ruby's `Data` class was introduced in Ruby 3.2, offering a convenient way to define value objects. The concept of value objects was popularized by Martin Fowler and Eric Evans through their books and articles. In the real world, we often represent properties like coordinates on a map `(x, y)` or the speed of a car—using primitives such as integers or strings. While these work, they lack the semantic clarity and behavior of custom types. This is where value objects shine. They encapsulate meaning and behavior around a set of values.

Ruby's `Data` class was created to provide a native way to represent such concepts. Struct does fullfil this requirement, except the part of immutability. Ruby Structs are mutable. Some example of use case for value objects are to represent money, email address, co-ordinates, etc.

Lets now explore ruby Data, keeping in my the characteristics of a value object as shared by Marin Fowler. A value object
should have:

- No Identity
- Immutable
- Equality by Value
- Small and Simple
- Reusable

Let's explore how Ruby's `Data` class supports these characteristics.

### class Data

`Data` is a core Ruby class, so no external gems are needed.

Here's a simple example:

```ruby
MarsRover = Data.define(:name, :x, :y)
```

<!--more-->

You can now initialize/use `MarsRover` like this:

```ruby
curiosity = MarsRover.new('Curiosity', 0, 0)
# or
curiosity = MarsRover['Curiosity', 0, 0]
# => #<data MarsRover name="Curiosity", x=0, y=0>
```

Create another instance:

```ruby
rover = MarsRover.new('Rover 2', 0, 0)
# => #<data MarsRover name="Rover 2", x=0, y=0>
```

These objects are immutable

```ruby
curiosity.name = 'New name'
# => NoMethodError: undefined method `name=' for an instance of MarsRover
```

and you can compare them

```ruby
curiosity == rover
# => false

rover = MarsRover.new('Curiosity', 0, 0)
curiosity == rover
# => true
```

### A More Practical Example

The Mars Rover example is abstract; here's a more practical one using location data:

```ruby
class Rover
  attr_accessor :name, :location

  def initialize(name, location)
    @name = name
    @location = location
  end
end

Location = Data.define(:x, :y)

rover1 = Rover.new('Curiosity'.freeze, Location[0, 0])
rover2 = Rover.new('Perseverance'.freeze, Location[0, 0])

rover1.location == rover2.location
# => true
```

You don’t need to compare `x` and `y` individually—value comparison is built-in.

### Updating Location with Immutability

You update the rover's position by replacing the `Location` object. This is exactly like how we would replace the integer
value x and y. But conceptually we are entering a new location which has x and y co-ordinates.

```ruby
Location = Data.define(:x, :y)

class Rover
  attr_accessor :name, :location

  def initialize(name, location)
    @name = name
    @location = location
  end

  def move_up = new_location(@location.x, @location.y + 1)
  def move_down = new_location(@location.x, @location.y - 1)
  def move_right = new_location(@location.x + 1, @location.y)
  def move_left = new_location(@location.x - 1, @location.y)

  private

  def new_location(x, y)
    @location = Location[x, y]
  end
end

rover1 = Rover.new('Curiosity'.freeze, Location[0, 0])
rover1.move_up
rover1.move_right
rover1.move_up

puts rover1.location
# => #<data Location x=1, y=2>
```

### Making `Data` Comparable

Ruby `Data` instances support `==` and `eql?` out of the box but not `<`, `>`, or `between?`. To enable this, include the `Comparable` module and define `<=>`:

```ruby
Location = Data.define(:x, :y) do
  include Comparable

  def <=>(other)
    [x, y] <=> [other.x, other.y]
  end
end
```

Now you can do:

```ruby
p1 = Location[0, 0]
p2 = Location[1, 1]

p1 < p2
# => true

p1.between?(Location[-1, -1], Location[1, 1])
# => true
```

This works with regular classes too:

```ruby
class Point
  include Comparable

  attr_accessor :x, :y

  def initialize(x, y)
    @x = x
    @y = y
  end

  def <=>(other)
    [x, y] <=> [other.x, other.y]
  end
end
```

### Adding Validations

Ruby’s `Data` does not support validation out of the box. However, you can override `initialize` with keyword arguments to add it:

```ruby
Location = Data.define(:x, :y) do
  def initialize(x:, y:)
    raise ArgumentError, "x and y must be numeric" unless x.is_a?(Numeric) && y.is_a?(Numeric)
    super
  end
end

Location.new('1', 2)
# => Raises ArgumentError
```

### Bonus: Using `dry-struct`

For more complete value object features, consider using [`dry-struct`](https://dry-rb.org/gems/dry-struct/main/). It adds type coercion and validation.

```ruby
require 'dry-struct'

module Types
  include Dry.Types()
end

class Location < Dry::Struct
  attribute :x, Types::Coercible::Integer
  attribute :y, Types::Coercible::Integer
end

point = Location.new(x: 1, y: 1)
```

### Conclusion

Ruby’s `Data` class makes it easy to define small, immutable, and comparable value objects with minimal effort. While it doesn’t support validations out of the box, it serves as a powerful and expressive tool for modeling semantics in your domain. When more control or strict typing is required, tools like `dry-struct` fill in the gaps. Whether you’re modeling locations, money, or any other composite data, value objects bring clarity and correctness to your code.

### Further Reading

- [Ruby 3.4 `Data` Documentation](https://docs.ruby-lang.org/en/3.4/Data.html)
- [Ruby `Comparable` Documentation](https://docs.ruby-lang.org/en/3.4/Comparable.html)
- [Martin Fowler – Value Object](https://martinfowler.com/bliki/ValueObject.html)
- [dry-struct Documentation](https://dry-rb.org/gems/dry-struct/main/)

