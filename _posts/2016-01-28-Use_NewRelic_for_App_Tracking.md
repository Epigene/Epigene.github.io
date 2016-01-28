---
layout: post
title: Use NewRelic for App Tracking
---
NewRelic tracks your app and provides info and graphs about throughput, slow requests, errors, etc.  
Follow this workflow to set it up for your Rails app.
You will need an account, [get one](https://newrelic.com/signup).

### 1. Add the gem to gemfile, bundle

```ruby
group :production, :staging do
  gem "newrelic_rpm"
end
```

### 2. Get clean newrelic.yml from website

Given account main view at https://rpm.newrelic.com/accounts/123,  
your yml should be at https://rpm.newrelic.com/accounts/123/newrelic.yml  

Use your account id number or search for "newrelic.yml" in the main view.

### 3. Place the fresh yml in apps `config/`

### 4. Edit the yaml (change name etc)

![newrelic]({{ site.baseurl }}/images/newrelic.png)
