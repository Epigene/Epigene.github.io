---
layout: post
title: Make Engines
---
In [previous post]({{ site.baseurl }}/Ruby:-Make-Gems-with-Bundler/) I discuessed ruby gem creation.  
In this post I will expound on creation of Rails engines - a variant of ruby gem that assumes inclusion in a rails app and, usually, provides functionality through models/controllers.  
Assumptions: Namespaced, RSpec+FactoryGirl, Postgres (at least in dev).  

### 1. Init engine project

```
$ rvm install ruby-<version>  e.g. $ rvm install ruby-2.2.3
$ rvm use 2.2.3@<gemset> --create
$ gem install bundler
$ gem install rails
$ mkdir ~/code/myengine
$ cd ~/code/myengine
$ rails plugin new . --mountable --dummy-path=spec/dummy -T --skip-bundle --database=postgresql
```

### 2. Edit myengine.gemspec, add dependencies

```ruby
  s.add_dependency "rails", "~> 4.1.0"
  s.add_dependency "pg", "~> 0.18.4"

  s.add_development_dependency "pry"
  s.add_development_dependency "rspec-rails"
  s.add_development_dependency 'factory_girl_rails'
```

### 3. Set up RSpec

```
# run the generator
$ rails g rspec:install

# add to .rspec
--format documentation
```

```ruby
# in /spec/rails_helper.rb

# Replace default test app location with the custom one
#default
require File.expand_path('../../config/environment', __FILE__)
# replace with this
require File.expand_path("../../spec/dummy/config/environment", __FILE__)

# Require development stuff
require 'pry'
require 'factory_girl'

# Load factories
ENGINE_RAILS_ROOT=File.join(File.dirname(__FILE__), '../')
Dir[File.join(ENGINE_RAILS_ROOT, "spec/factories/**/*.rb")].each {|f| require f }
```

```ruby
# in /lib/myengine/engine.rb

module Myengine
  class Engine < ::Rails::Engine
    isolate_namespace DevelopmentMail

    config.generators do |g|
      g.test_framework :rspec
      g.integration_tool :rspec
    end
  end
end
```

```
# Prepare database
$ rake db:create db:migrate RAILS_ENV=test

# test functionality
$ rspec
```

### 4 Handle migrations
Ensure engine users run

```
$ rake myengine:install:migrations

# or the get-all version
$ rake railties:install:migrations
```

### 5. Set git remote using `git remote add origin <url>`

```
$ git remote add origin https://github.com/CreativePublisher/engine_name.git
```

### 6. Use cool configuration object via https://robots.thoughtbot.com/mygem-configure-block
```ruby
module Myengine

  class << self
    attr_accessor :config
  end

  def self.configure
    self.config ||= Config.new
    yield(config)
  end

  class Config
    attr_accessor :some_attribute

    # optional initialize override to pass default values
    # def initialize
    #   @some_attribute = "default goodness"
    # end
  end

end
```

Usage

```ruby
Myengine.configure do |config|
  config.some_attribute = "peaches"
end

Myengine.config #=> Config object
Myengine.config.some_attribute #=> "peaches"
Myengine.config.some_attribute = "candy"
Myengine.config.some_attribute #=> "candy"
```


## Local Development

1. Get the gem locally (you probably have it if you are the developer)

2. In main app, make some folder for linked engines, for example `/engines`

3. Link the local gem into engines

```
sudo ln -Ffs ~/path/to/myengine ~/path/to/main_app/engine/myengine
```

4. Configure gemfile to use the local version

```ruby
gem 'myengine', path: 'engines/myengine'
```

![Engine]({{ site.baseurl }}/images/engine.png)
