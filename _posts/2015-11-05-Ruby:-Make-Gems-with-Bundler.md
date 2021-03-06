---
layout: post
title: Make Gems with Bundler
---
### 1. Init gem project

```
$ rvm install ruby-<version>  e.g. $ rvm install ruby-2.2.3
$ rvm use 2.2.3@<gemset> --create
$ gem install bundler
$ gem install pry
$ bundle gem <gemname>
```

### 2. Fill out \<gemname\>.gemspec, especially descriptions

```ruby  
# Only with ruby 2.0.x
spec.required_ruby_version = '>= 2'
spec.date                  = Date.today.to_s
```

### 3. Setup dev and runtime dependencies in \<gemname\>.gemspec file, RSpec for example

```ruby  
s.add_development_dependency "rspec-rails"
# s.add_development_dependency 'factory_girl_rails'

s.add_dependency 'activesupport'
```

### 4. Setup RSpec

```
$ rspec --init

# Create a spec, for example spec/foobar_spec.rb:
mkdir spec/tests
touch spec/tests/foo_spec.rb

# rspec spec/tests/foo_spec.rb
describe "Gem_Name" do
  pending "write it"
end

# add to .rspec
--format documentation
--require spec_helper

# add requires to spec_helper.rb
require 'simplecov'
SimpleCov.start
...
require 'timecop'
require 'pry'

# Run spec
$ rspec spec/tests/foo_spec.rb
```

### 5. Set git remote using `git remote add origin <url>`

```
# git@github.com:<username>/<project>.git
$ git remote add origin git@github.com:Epigene/myblog.git
```

### 6. Get hackin', add ROOT constant for navigation

```rb
module MyGem
  extend self

  def root
    @@root ||= Pathname.new(Gem::Specification.find_by_name("my_gem").gem_dir)
  end
end
```

### 7. Once everything is TDDed, bump the version and `rake relase`

### 8. Troubleshooting

Sometimes `rake release` will hang.  
Do a `gem push` first to get rubygems creditentials set up.  


![Rubygems]({{ site.baseurl }}/images/rubygems.png)
