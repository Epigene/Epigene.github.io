---
layout: post
title: Use Method Missing
---
```ruby
@@method_missing_regex = /^is_(.*)\?$/

def method_missing(method_sym, *arguments, &block)
  # the first argument is a Symbol, so you need to_s it if you want to pattern match
  if method_sym.to_s[@@method_missing_regex]
    # If the method missing matches the pattern, you can access the capture group with $1
    # for example @object.is_badass? #=> "badass"
    puts $1
  else
    super
  end
end

def self.respond_to?(method_sym, include_private = false)
  if method_sym.to_s[@@method_missing_regex]
    true
  else
    super
  end
end
```

[Reference](http://technicalpickles.com/posts/using-method_missing-and-respond_to-to-create-dynamic-methods/)

![method_missing.jpg]({{ site.baseurl }}/images/method_missing.jpg)
