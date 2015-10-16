---
layout: post
title: Follow Model Best Practices
---
Say you want to keep your model definitions clean and consistent.  
Do this:

```ruby
class User < ActiveRecord::Base
  # use this class method in strong parameters
  # params.require!(:user).permit(User.params)
  def self.params
    %i|email origin_email name surname|
  end

  # constants
  COLORS = %w(red green blue)

  # afterwards we put attr related macros
  attr_accessor :formatted_date_of_birth

  # Do not use attr_accessible anymore, migrate to strong parameters
  # attr_accessible :login, :first_name, :last_name, :email, :password  

  # enums
  enum gender: { female: 0, male: 1 }

  # followed by association macros
  belongs_to :country

  has_many :authentications, dependent: :destroy

  # and validation macros  
  validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true }
  validate  :dat_custom_validation

  # next we have callbacks
  before_save :cook
  before_save :update_username_lower  

  # other macros
  # ..

  # ..

  # finish up with named scopes
  scope :adult, -> { where("age > ?", 17) }
  scope :named, ->(first_name) { where(name: first_name) }

  # add methods below
end
```

More at [Rails style guide](https://github.com/bbatsov/rails-style-guide)  

![Hash]({{ site.baseurl }}/images/clean_guidelines.png)
