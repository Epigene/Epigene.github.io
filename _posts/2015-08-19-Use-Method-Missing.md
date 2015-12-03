---
layout: post
title: Use Method Missing
---
You can override Ruby's `method_missing` method to make dynamic methods based on regex.  
For example, a trivial case, User can have three string gender settings: `"male", "female" and "other"` and you want checker methods of the style `gender_male? gender_female? gender_other?`.  
It is not DRY to write these three repetetive methods. Use method_missing!  
Follow this pattern using case, that way you can add several `when` clauses to provide different regex-matched dynamic methods (make sure they do not overlap!)

```ruby
def method_missing(method_sym, *arguments, &block)
  # the first argument is a Symbol, so you need to_s it if you want to pattern match
  case method_sym.to_s
  when /gender_(.*)\?/
    return $1 == gender
  else
    super
  end

end

def respond_to?(method_sym, include_private = false)
  case method_sym.to_s
  when /gender_(.*)\?/
    true
  else
    super
  end
end
```

[Reference](http://technicalpickles.com/posts/using-method_missing-and-respond_to-to-create-dynamic-methods/)

![method_missing.jpg]({{ site.baseurl }}/images/method_missing.jpg)
