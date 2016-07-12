---
layout: post
title: Arguments in rake task
---

#### Method 1, environmental variables
```ruby
desc "Example of a multi-argument raketask via environmental variables"
# rake with_environmentals name=Liene surname=Viktus
task with_environmentals: :environment do
  name = ENV['name']
  surname = ENV['surname']
  print name; print " "; print surname
end
```

#### Method 2, named argument hash
```ruby
desc "Example of a multi-argument raketask via argument hash"
# rake with_hash  #=> John Smith
# rake with_hash[Chris,Brown] #=> Chris Brown
task :with_hash, [:name, :surname] => :environment do |t, hash|
  hash.with_defaults(name: :John, surname: :Smith)
  puts "Arguments with defaults were: #{hash}"
  print hash.name; print " "; print hash.surname
end
```

#### Bonus Method 3, anonymous argument hash
```ruby
desc "Bring it on, parameters!"
# rake infinite_paramers['The','World','Is','Just','Awesome','Boomdeyada']
# via http://stackoverflow.com/a/28654953/3319298
task infinite_parameters: :environment do |task, args|
    puts args.extras.count
    args.extras.each do |params|
        puts params
    end         
end
```

![rake.jpg]({{ site.baseurl }}/images/rake-04.jpg)
