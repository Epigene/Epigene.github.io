---
layout: post
title: Unusual Ruby hash keys
---
Ruby hashes can have true/false keys

```ruby
a = Hash.new(0) #=> {}
a[false] = 10
a[true] = 9
a[false]- a[true] #=> 1
```

Furthermore, Ruby hashes can have *any* object as a key

```ruby
a = Hash.new(0) #=> {}
a[{}] = "Hash" # Hash object serving as a key.
a[1] = "Integer" # Integer serving as a key
a[ArgumentError.new] = "Doh!" # Error class serving as a key

a #=>
# {
#                                {} => "Hash",
#                                 1 => "Integer",
#   #<ArgumentError: ArgumentError> => "Doh!"
# }
```

![keys.jpg]({{ site.baseurl }}/images/keys.jpg)
