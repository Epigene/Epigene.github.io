---
layout: post
title: Quick and simple Rails basic-auth with user support
---
1. Create a User model

```ruby
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :first_name, null: false
      t.string :last_name, null: false
      t.string :login, null: false
      t.string :password, null: false
    end
  end
end
```

2. Create users in seeds or a rake file:

```ruby
User.where(first_name: "Test", last_name: "User", login: "login", password: "password").first_or_create
```

3. Add a method to application controller

```ruby
protected
  def authenticate
    authenticate_or_request_with_http_basic do |login, password|
    @user = User.where(login: login, password: password).first
    @user.present? ? true : false
  end
```

4. Call method in a `before_filter` where necessary

```ruby
before_filter :authenticate, except: [:index, :show]
```

![rake.jpg]({{ site.baseurl }}/images/auth-01.png)

###Discussion:

1. In step 3, the `authenticate_or_request_with_http_basic` block is expected to return true for authentication.
  You may make it even simpler by ignoring the login or password. Or both by doing
  `authenticate_or_request_with_http_basic { @user = User.last; true }`.
2. Doing so will automagically login a specific user (set the `@user` variable). This may be useful for development.
