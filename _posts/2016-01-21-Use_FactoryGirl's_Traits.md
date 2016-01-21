---
layout: post
title: Use FactoryGirl's Traits
---
## Define a solid base factory with all appropriate traits

```ruby
FactoryGirl.define do
  factory :todo_item, aliases: [:incomplete_todo_item] do
    # default attribute values
    name 'Pick up a gallon of milk'
    complete false

    # trait definitions
    trait :completed do
      complete true
    end

    trait :not_completed do
      complete false
    end

    trait :with_comments do
      after(:create) do |todo|
        create_list(:comment, 2, todo_item: todo)
      end
    end

  end
end
```

## Use the base factory as a convenient parent

```ruby
FactoryGirl.define do
  factory :construction_todo_item, parent: :todo_item do
    # default attribute values
    name 'Pick up drill'    

    # domain-specific trait definitions
    trait :cement do
      name 'Pick up cement'
      amount '50kg'
    end

  end
end
```


## Mix and match traits as you please:

```ruby
create(:todo_item, :completed, :with_comments)
create(:todo_item)
create(:todo_item, :not_completed, name: 'Pick up a bag of sugar')
create(:construction_todo_item, :not_completed, :cement, amount: "100kg")
# But not
# create(:todo_item, :cement) # since :cement trait is only defined for construction todos
```

## Use traits in child factory definitions

```ruby
FactoryGirl.define do
  factory :large_cement_todo_item, parent: :construction_todo_item do
    cement # will set :name and :amount accordingly
    amount "200kg" # override amount set in trait
  end
end
```

Originally at [Thoughtbot Blog](https://robots.thoughtbot.com/remove-duplication-with-factorygirls-traits).

![traits]({{ site.baseurl }}/images/traits.jpg)
