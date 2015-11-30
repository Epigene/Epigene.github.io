---
layout: post
title: Use Delegations
---
Recently I received a [link to an opinionated blog post](https://medium.com/@gioch/design-patterns-law-of-demeter-with-rails-49a44a9689fe) about how to observe the law of demeter in Rails. It strongly suggests using delegations in Rails models, and here is the __TL;DR__ of how you do it. This has also been added to the [rails guidelines post](http://epigene.github.io/Rails4:_Follow_Model_Best_Practices)

```ruby
class User < ActiveRecord::Base
  # law of demeter conserving delegations
  delegate :id, :name to: :case, allow_nil: true, prefix: "buddy" #=> .buddy_id
  delegate :gifts, to: :friend, prefix: false #=> .gifts
end
```

These two delegation lines add three methods to User instances

```ruby
@user.buddy_id # calls @user.case.id, returns nil if no associated case is found
@user.buddy_name # calls @user.case.name, returns nil if no associated case is found

@user.gifts # calls @user.friend.gifts, throws an error if no associated friend
```

As a rule of thumb, you want to define delegations for these scenarios:

```ruby
@model.belongs_to_association.attribute
@model.has_one_association.attribute
```

![delegate]({{ site.baseurl }}/images/delegate.png)
