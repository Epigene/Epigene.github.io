---
layout: post
title: Make Engines
---
In [previous post]({{ site.baseurl }}/Ruby:-Make-Gems-with-Bundler/) I discuessed ruby gem creation.  
In this post I will expound on creation of Rails engines - a variant of ruby gem that assumes inclusion in a rails app and, usually, provides functionality through models/controllers.  
Assumptions: Namespaced, RSpec+FactoryBot, Postgres (at least in dev).  

See also [this post](https://www.viget.com/articles/rails-engine-testing-with-rspec-capybara-and-factorygirl/).

### 1. Init engine project

```
# rvm install ruby-<version>
rvm install ruby-2.3.7

# rvm use 2.3.7@<gemset> --create
rvm use 2.3.7@myengine --create

gem install bundler

# gem install rails -v <version>
gem install rails -v 4.2.10

# make a directory that matches your intended engine name
mkdir ~/code/myengine
cd ~/code/myengine

# run the opinionated initializer, will use spec/dummy for test app and Postgres database
rails plugin new . --mountable --dummy-path=spec/dummy -T --skip-bundle --database=postgresql
```

### 2. Edit myengine.gemspec, add dependencies and bundle

```ruby
  s.required_ruby_version = '>= 2.3.7'

  s.test_files = Dir["spec/**/*"]

  # ...

  s.add_dependency "rails", "~> 4.2.10"

  s.add_development_dependency "rake", "~> 10.5.0"  
  s.add_development_dependency "pg", ">= 0.18.4", "< 1"

  s.add_development_dependency "pry"
  s.add_development_dependency "rspec-rails", "~> 3.8"
  s.add_development_dependency 'factory_girl_rails', "~> 4.10"
  s.add_development_dependency 'timecop', "~> 0.8.1"
  s.add_development_dependency "spring-commands-rspec", "~> 1.0.4"

```

### 3. Set up RSpec

```
# run the generator
rails g rspec:install

# make .rspec

--format documentation
--require rails_helper
--color
```

```ruby
# in /spec/rails_helper.rb

# Replace default test app location with the custom one
#default
require File.expand_path('../../config/environment', __FILE__)
# replace with this
require File.expand_path("../dummy/config/environment", __FILE__)

# Require development stuff
require 'myengine' # the engine itself, lol
require 'rspec/rails' # very importnt, explicity require all rspec goodies like config block and controller helpers
require 'pry'
require 'factory_girl'
require 'timecop'

# Define engine root
ENGINE_ROOT = File.expand_path("..", File.dirname(__FILE__))
require "#{ENGINE_ROOT}/spec/dummy/config/environment"

# load factories (using root)
Dir[File.join(ENGINE_ROOT, "spec/factories/**/*.rb")].each {|f| require f }


RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end

  config.infer_base_class_for_anonymous_controllers = true
  config.infer_spec_type_from_file_location!
end
```

```ruby
# in /lib/myengine/engine.rb

module Myengine
  class Engine < ::Rails::Engine
    isolate_namespace Myengine

    config.generators do |g|
      g.test_framework :rspec
      g.integration_tool :rspec
      g.fixture_replacement :factory_girl, :dir => 'spec/factories'
      g.assets false
      g.helper false
    end
  end
end
```

```
# Prepare database
$ rake db:create db:migrate RAILS_ENV=test

# set up spring
# in /myapp/config/spring.rb
Spring.application_root = "#{File.expand_path(__FILE__)}/../../spec/dummy"

# test functionality
$ spring rspec
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

### 7. Give your engine a shorthand namespace

```
# in /myengine/lib/myengine.rb

# ... add as last lines
ME = Myengine # now you can use ME::SomeModel instead of Myengine::SomeModel
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
