---
layout: post
title: Use Delegations
---
You want to keep track of your app's load times.  
[Mini Profiler](https://github.com/MiniProfiler/rack-mini-profiler) is excellent for this.

Basic setup is super easy.  

### 1. Add the gem and bundle

```ruby
gem 'rack-mini-profiler'
```

### 2. for production
Add to `application_controller.rb`

```ruby
before_action do
  # change `current_admin.present?` to any check method you like
  if current_admin.present?
    Rack::MiniProfiler.authorize_request
  end
end
```

![mini profiler]({{ site.baseurl }}/images/mini_profiler.png)
