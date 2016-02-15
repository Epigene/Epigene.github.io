Something loads forever. What gives?  
These are the tools we use to debug performance issues.

========= Deep performance testing stack =========
group :development do
  gem 'meta_request' # + Rails Panel for Chrome
  gem "bullet"

  gem 'rack-mini-profiler'
  gem 'flamegraph'
  gem 'stackprof' # ruby 2.1+ only
  gem 'memory_profiler'
end

## MetaRequest
Simply include the gem and use the pair app Rails Panel in chrome

## Bullet
You will need to put some config in `development.rb` environment file for Bullet to do __anything__.
[Consult their readme](https://github.com/flyerhzm/bullet#configuration).

Or use a minimal pre-config that will silently log things to /log/bullet.log

```ruby
config.after_initialize do
  Bullet.enable = true
  Bullet.bullet_logger = true
  Bullet.counter_cache_enable = false
end
```

## Rack Mini Profiler

Consult the [detailed blog post](https://www.nateberkopec.com/2015/08/05/rack-mini-profiler-the-secret-weapon.html)

# Custom sub-accociations on models
consult [preloading blog post](http://blog.arkency.com/2013/12/rails4-preloading/)

```ruby
# in model
  has_many :polish_addresses, -> {where(country: "Poland")}, class_name: "Address"

# in controller
User.includes(:polish_addresses) # will preload only associated polish addresses, not all addresses.
```
