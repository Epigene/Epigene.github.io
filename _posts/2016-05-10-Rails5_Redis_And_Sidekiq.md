---
layout: post
title: Rails5: Redis + Sidekiq
---
Here is my workflow for setting up sidekiq as the background job processing adapter for Rails5.  

### 1. Include the gem

```ruby
gem 'sidekiq', "~> 4.1.2"
```

### 2. Set :sidekiq as ActiveJob adapter

```ruby
# in /config/application.rb
class Application < Rails::Application
  # ...
  config.active_job.queue_adapter = :sidekiq
end
```

### 3. Configure redis

```ruby
# in config/initializers/sidekiq.rb

# Configuration
raise "Expected environment to contain REDIS_URL, but it does not!" if !(Rails.env.development? || Rails.env.test?) && ENV["REDIS_URL"].blank?

redis = Hash.new{|h, k| h[k] = Hash.new(config: {url: ENV["REDIS_URL"], port: URI.parse(ENV["REDIS_URL"]).port}) }
redis["development"] = {config: {url: "redis://localhost:6390"}, port: "6390"}
redis["test"] = {config: {url: "redis://localhost:6391"}, port: "6391"}

# Boot local redis in dev
if Rails.env.development? || Rails.env.test?
  system("redis-server --port #{redis.dig(Rails.env, :port)} --daemonize yes")
  raise "Couldn't start redis" if $?.exitstatus != 0
end

# Uses the hash to configure server and client
Sidekiq.configure_server do |config|
  #config.redis = { url: 'redis://redis.example.com:7372/12' }
  config.redis = redis.dig(Rails.env, :config)
end

Sidekiq.configure_client do |config|
  #config.redis = { url: 'redis://redis.example.com:7372/12' }
  config.redis = redis.dig(Rails.env, :config)
end

# WEB UI
require 'sidekiq'
require 'sidekiq/web'

Sidekiq::Web.use(Rack::Auth::Basic) do |user, password|
  [user, password] == ["creomobi", "cre@tive:)"]
end

REDIS = Redis.new(url: redis.dig(Rails.env, :config, :url), port: redis.dig(Rails.env, :config, :port))
puts ">> Initialized REDIS with #{REDIS.inspect}"
```

### 4. Add the Web UI route

```ruby
require 'sidekiq/web'

Rails.application.routes.draw do
  mount Sidekiq::Web => '/sidekiq'
end
```

### 4. Generate a job

```
rails generate job Example
```

### 5. populate to job's perform method

```ruby
class ExampleJob < ActiveJob::Base
  # Set the Queue as Default
  queue_as :default

  def perform(*args)
    user = User.find(args.first)
    user.some_method
  end
end
```

### 6. Enqueue the job!

```ruby
ExampleJob.perform_later(args)
```

### 7. Run the worker
#### Locally

```
bundle exec sidekiq -e development
```

#### Production

```
# in Procfile
worker: bundle exec sidekiq -c 10 -q priority -q default
```

There's more at [wiki](https://github.com/mperham/sidekiq/wiki/Advanced-Options)

### 7. Troubleshooting
Consult [the wiki](https://github.com/mperham/sidekiq/wiki/Active+Job)

![job]({{ site.baseurl }}/images/sq.png)