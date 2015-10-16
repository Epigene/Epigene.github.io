---
layout: post
title: Use Global Scopes
---
Say you want all your models to have a scope that lets you select them by date of their `created_at`.

1. Make a module

```ruby
# in /app/models/concerns/global_scopes.rb
module GlobalScopes
  def self.included(base)
    base.class_eval do
      def self.in_daterange(start_date, end_date)
        all.where(created_at: start_date.to_date.beginning_of_day..end_date.to_date.end_of_day)
      end
    end
  end
end
```

2. Have the module included in ActiveRecord::Base.

```ruby
# in /config/initializers/activerecord.rb
ActiveRecord::Base.send(:include, GlobalScopes)
```

More at [github](http://stackoverflow.com/a/32710395/3319298)

![Global]({{ site.baseurl }}/images/global.png)
