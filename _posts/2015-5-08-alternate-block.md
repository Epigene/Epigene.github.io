---
layout: post
title: Ruby block variants
---
Ruby blocks come in two variants, the `{|vars| #code }` and the `do |vars| #code end`.

Once you realize that these are both equivalent blocks, you can do either `(1..3).map{|n| n}.inject(:+)` or

```ruby
(1..3).map do |n|
n
end.inject(:+)
```

Originally at [http://stackoverflow.com/questions/1341271/how-do-i-create-an-average-from-a-ruby-array]

![r_blocks.jpg]({{ site.baseurl }}/images/r_blocks.jpg)