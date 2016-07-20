---
layout: post
title: (Rails5) Redis and Sidekiq
---
Here is my workflow for setting up sidekiq as the background job processing adapter for Rails5.  

### 1. Include the gem

```ruby
gem 'sidekiq', '~> 4.1.4'  # the hard-hitting background worker
gem 'sinatra', '~> 1.4.7', require: false # for sidekiq's web UI
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
# in config/initializers/redis.rb

# Configuration
redis = Hash.new{|h, k| h[k] = Hash.new(url: ENV["REDIS_URL"].presence || "redis://localhost:6379")}

# redis["production"] = {url: ENV["REDIS_URL"].presence || "redis://localhost:6390"}
# redis["staging"] = {url: ENV["REDIS_URL"].presence || "redis://localhost:6390"}
redis["development"] = {url: "redis://localhost:6390"}
redis["test"] = {url: "redis://localhost:6391"}

# Parse the env-specific url
uri = URI.parse(redis.dig(Rails.env, :url))

# Boot local redis in dev
if Rails.env.development? || Rails.env.test?
  system("redis-server --port #{uri.port} --daemonize yes")
  raise "Couldn't start redis" if $?.exitstatus != 0
end

# Initialize application-wide constant.
REDIS = Redis.new(url: uri.to_s, port: uri.port).freeze
puts ">> Initialized REDIS with #{REDIS.inspect}"
```

### 4. Configure sidekiq

```ruby
# in config/initializers/sidekiq.rb

# Depends on redis.rb having run and initialized REDIS constant.

# WEB UI
require 'sidekiq'
require 'sidekiq/web'

Sidekiq::Web.use(Rack::Auth::Basic) do |user, password|
  [user, password] == ["creomobi", "cre@tive:)"]
end

redis_url = URI.parse(URI::extract(REDIS.inspect).first).to_s
config_hash = {url: redis_url}

Sidekiq.configure_server do |config|
  config.redis = config_hash
end

Sidekiq.configure_client do |config|
  config.redis = config_hash
end
```

### 5.0 Prepare Job inheritance
Rails5 defines a `ApplicationJob` class that acts as an abstract parent class for all your jobs.  

```rb
class ApplicationJob < ActiveJob::Base
end
```

Then have your jobs inherit from it to have shared logic.  

```rb
class TestJob < ApplicationJob
end
```

### 5.1 Generate a job

```
rails generate job Example
```

### 6. populate to job's perform method

```ruby
class ExampleJob < ApplicationJob
  # Set the Queue as Default
  queue_as :default

  def perform(*args)
    user = User.find(args.first)
    user.some_method
  end
end
```

### 7. Enqueue the job!

```ruby
ExampleJob.perform_later(args)

# or change queue on the fly
ExampleJob.set(queue: :another_queue).perform_later(args)
```

### 8. Run the worker

#### Locally

```
bundle exec sidekiq -e development
```

#### Production

```
# in Procfile
worker: bundle exec sidekiq -c 10 -q priority -q default
```

There's more about running Sidekiq at [wiki](https://github.com/mperham/sidekiq/wiki/Advanced-Options)

### 9. Troubleshooting
Consult Sidekiq [wiki](https://github.com/mperham/sidekiq/wiki/Active+Job)
See also Rails ActiveJob [documentation](http://guides.rubyonrails.org/active_job_basics.html)

![job]({{ site.baseurl }}/images/sq.png)
