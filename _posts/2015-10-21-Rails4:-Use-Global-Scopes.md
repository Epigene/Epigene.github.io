---
layout: post
title: Use Global Scopes
---
Say you want all your models to have a scope that lets you select them by date of their created_at.

1. Make a module right under `/models/concerns/global_scopes.rb`
```ruby
module GlobalScopes
  extend ActiveSupport::Concern

    included do      
      scope :in_daterange, ->(start_date, end_date) { where(created_at: start_date.to_date.beginning_of_day..end_date.to_date.end_of_day) }
    end

end
```

2. Have this module extend Activerecord::Base. Put in an initializer.
```ruby
ActiveRecord::Base.class_eval do
  include GlobalScopes
end

```

![Global]({{ site.baseurl }}/images/global.png)
