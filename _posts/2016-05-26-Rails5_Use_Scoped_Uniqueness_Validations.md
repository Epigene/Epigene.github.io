---
layout: post
title: (Rails5) Use Scoped Uniqueness Validations
---
Consider this: we have an aggregation app, that receives data from other apps.  

```ruby
class Event < ApplicationRecord
  # Scheme  
  # t.integer :origin_app, null: false, default: 0, index: true
  # t.integer :origin_type, null: false, default: 0, index: true
  # t.integer  :origin_id, null: false, index: true  

  enum origin_app: { old_app: 0, new_app: 1 }
  enum origin_type: { good: 0, bad: 1 }

  validates :origin_app, :origin_type, :origin_id, presence: true
end
```

We want to validate `:origin_id` uniqueness within an app and type.

### 1. DB Level

Add a compound uniqueness index in migration.  

```ruby

# add_index :<table>, [:<validatable field>, :<scope_field_1>, .. , :<scope_field_n>], unique: true

# e.g.
add_index :admin_events, [:origin_id, :origin_app, :origin_type], unique: true
```

### 2. App Level

Add a uniqueness validation with scope option.  

```ruby
class Event < ApplicationRecord  
  # ...
  validates :origin_id, uniqueness: {scope: [:origin_app, :origin_type]}  
end
```

### Troubleshooting

Consult [the docs](http://guides.rubyonrails.org/active_record_validations.html#uniqueness).

![job]({{ site.baseurl }}/images/scoped.jpg)
