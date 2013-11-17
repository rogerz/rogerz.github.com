---
layout: post
title: "Inside 'Hello world' of Sinatra"
comments: true
categories: ruby
published: true

---

[sinatra](http://www.sinatrarb.com/intro.html) is claimed to be a DSL for creating web application. So how is this **DSL** made up. Let's dive into the hello world example and see.

# General

```
# myapp.rb
require 'sinatra'

get '/' do
  'Hello world!'
end
```

After requiring `sinatra`, a new method `get` is available for the `main` Object.

# Inside

## require

`sinatra.rb` does nothing but requires some other files. `require` will execute all the statement in the target file.

```
# sinatra.rb

require 'sinatra/base'
require 'sinatra/main'
```

## extend

It is in the `main.rb` that extends the method from Sinatra::Delegator

```
# sinatra/main.rb

extend Sinatra::Delegator
```

For the difference between `extend` and `include`, here is a good [explanation](http://rubyquicktips.com/post/1133877859/include-vs-extend)

>include makes the module’s methods available to the instance of a class, while
>extend makes these methods available to the class itself.

## define_method

It is in `base.rb` where `Sinatra::Delegator` is defined

```
# sinatra/base.rb

module Sinatra
    module Delegator
        def self.delegate(*methods)
            methods.each do |method_name|
                define_method(method_name) do |*args, &block|
                    return super(*args, &block) if respond_to? method_name
                    Delegator.target.send(method_name, *args, &block)
                    ...
                end
            end
        end

        delegate :get,:patch

        class << self
      	    attr_accessor :target
        end

    ​    self.target = Application
    end
end
```

# Outline

The original code looks a bit complicated. In short, it could be

```
# methods.rb
module Methods
    define_method(:get) do |*args, &block|
        puts "get is called"
    end
end

extend Methods
```

```
require 'methods'

get '/' do
  "hello, world"
end
```
