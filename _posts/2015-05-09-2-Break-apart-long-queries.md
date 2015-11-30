---
layout: post
title: Break apart long queries
---
Complex queries can be chained like this

```ruby
base = Transaction.joins(order: {page: :pipeline})
      .on_date(date) # Ensures day dimension
      .where(transactions: {provider: provider}) # Ensures matching provider
      .where(pipelines: {id: pipeline_id}) # Ensures matching pipeline
```


