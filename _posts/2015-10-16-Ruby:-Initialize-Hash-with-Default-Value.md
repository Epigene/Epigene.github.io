---
layout: post
title: Initialize Hash with Default Value
---
Say you want a hash to return some programmatic value based on the key called without assigning the return value explicitly.  

Passing a block to Hash.new specifies what happens when you ask for a non-existent key.

```ruby
# new {|hash, key| block } → new_hash
# it is the blocks responsibility to save the value into the hash, if necessary

default_hash = Hash.new{|hash, key| key.to_i.even? ? "even!" : "odd!"}
default_hash #=> {}
default_hash[1] #=> "odd!"
default_hash #=> {} # did not save the call

default_hash = Hash.new{|hash, key| value = (key.to_i.even? ? "even!" : "odd!"); hash[key] = value}
default_hash #=> {}
default_hash[1] #=> "odd!"
default_hash #=> {1 => "odd!"} # saved
```

More at:
[fun with ruby hashes](https://netinlet.com/blog/2014/01/01/fun-with-ruby-hashes/)
[algorithmic fun with ruby hashes](http://www.sitepoint.com/algorithmic-fun-ruby-hashes/)
[solving antipatterns](http://www.sitepoint.com/how-to-solve-coding-anti-patterns-for-ruby-rookies/)

![Hash]({{ site.baseurl }}/images/hash.jpg)
