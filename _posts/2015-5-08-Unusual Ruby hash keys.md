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

![keys.jpg]({{ site.baseurl }}/images/keys.jpg)
