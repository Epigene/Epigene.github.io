---
layout: post
title: Rails4 Use concern namespaces
---

#### 1 Set up your rails 4 app to load subfolders of /app/models/concerns

```ruby
# /config/application.rb
class Application < Rails::Application
  config.autoload_paths += Dir["#{Rails.root}/app/models/concerns/**/*.rb"]
end
```

#### 2 Set up subfolders.
Say you have a `User` model and you want to refacture validations into a module for it.

```sh
mkdir /app/models/concerns/user
touch /app/models/concerns/user/validations.rb
```

#### 3 Require this module in User model definition

```ruby
# /app/models/user.rb

require_dependency 'user/validations' # this may be necessary if dependency breakage occurs
class User < ActiveRecord::Base
  include User::Validations
end
```

#### 4 Place module definition and some validations in /app/models/concerns/user/validations.rb

```ruby
class User < ActiveRecord::Base
  module Validations
    extend ActiveSupport::Concern

    included do
      validates :email, uniqueness: {message: "Email already taken!"}, presence: {message: "Email must be present!"}
      validates_format_of :email, with: /\A.*?\@.*?\Z/i, message: "Weird email"
    end

    module ClassMethods
      def some_class_method
        # code
      end
    end

    def some_instance_method
      # code
    end
  end
end
```

## It is OK to have a User model definition looking like:

```ruby
class User < ActiveRecord::Base
  include User::Validations
  include User::Decorators
  include User::ClassMethods
  include User::ThatCoreBusinessLogic
  include User::Messaging

  scope :adults, -> { where(age: 18..999) }
  scope :children, -> { where(age: 0..17) }

end
```

![inside.jpg]({{ site.baseurl }}/images/inside.jpg)
