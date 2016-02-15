If you find yourself calling Model.associations.size a lot, you may gain some performance improvement by adding a `counter_cache` column to the Model.
Adding it is not difficult, but there are several gotchas.  
Follow along carefully

## Given this belongs_to->has_many association

class Post < ActiveRecord::Base
  has_many :comments
end

class Comments < ActiveRecord::Base
  belongs_to :post
end

## 1. Add the column via migration
### Generate the migration file

```
rails g migration AddCommentsCounterCacheToPosts
```

### Populate the migration file

Name the column with the plural name of the has_many association followed by `_count` i.e. `comments_count`.  
Make sure it is an integer column.  
Make sure it defaults to zero and does not allow nil.
Indexing may be useful, optional.

```ruby
class AddCommentsCounterCacheToPosts < ActiveRecord::Migration
  def change    
    add_column :posts, :comments_count, :integer, index: true, null: false, default: 0    
  end
end
```

## 2. Enable Rails' automatic incrementing of the counter

```ruby
# in /models/comment.rb
  belongs_to :post, counter_cache: true
```

## 3. Fix existing association count
### Write a rake task that handles existing association recounting

```ruby
namespace :maintenance do
  desc 'Recalculates all counter caches'
  # rake maintenance:sync_counter_caches
  task "sync_counter_caches" => :environment do
    Post.all.pluck(:id).each{|post_id| Post.reset_counters(post_id, :comments) }
    puts "All recounted!"
  end
end
```

### Run the task

```
rake maintenance:sync_counter_caches
```
