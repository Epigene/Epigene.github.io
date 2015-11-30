---
layout: post
title: Deep nested queries in Activerecord
---
Contributed the backend of a statistics panel today.

![rake.jpg]({{ site.baseurl }}/images/statistics_panel.png)

Wrote complex, deep-nested queries for associated object data in Activerecord, for example

```ruby
@base_query = Payment.joins(order: {page: :window}).in_daterange(@start_date, @end_date).where("windows.id = ?", window.id).where("provider = ?", @payment_system.provider)
```

![rake.jpg]({{ site.baseurl }}/images/mat.jpg)

As you can see, I use a custom scope `in_daterange(@start_date, @end_date)` that takes two arguments. Here is the definition

```ruby
scope :in_daterange, ->(start_date, end_date) { where(created_at: start_date.to_date.beginning_of_day..end_date.to_date.end_of_day) }
```

For calculating percentages, handling 0/0 cases is important, here is a helper for that

```ruby
def calculate_percentage divisible, divisor
  return (divisible.to_f / (divisor.to_f + Float::EPSILON) * 1000).ceil / 10.0
end
```

Good day, learned some useful things.
