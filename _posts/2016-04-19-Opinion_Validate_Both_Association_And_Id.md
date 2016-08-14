---
layout: post
title: "Opinion: Validate Both Association And Id"
---
I've beef with some of the validation recommendations out there.

### Disagreeable Ideas
`belongs_to` validation strategy espoused in [this article](http://railsguides.net/belongs-to-and-presence-validation-rule1/).  

I strongly disagree with:  

> Use association name for presence validator. (as opposed to :association_id)

### My Recommendation
I will be mirroring a notion expressed [here](http://naildrivin5.com/blog/2015/11/15/rails-validations-vs-postgres-check-constraints.html), namely

> Think about Rails validations as purely something for user experience.  
> Think about database constraints as just being about data integrity.

To me it is essential that I never get database errors. In my opinion, correct application code should handle 99.99% of validation errors.  

I have a threefold recommendation. With these three layers in place your app will be nigh-impenetrable to bad data while providing actionable validation errors transparent to both users and developers.  

#### 1. Use Database-level constraints
This is simple redundancy that will spare you a lot of headache.  
__All records in your relational database should always be valid, period.__

Define your model migrations with appropriate `:null`, `:default`, `:index`, and `:unique` options!  
Define sensible default values that match the field type!  

```rb
t.references :user, null: false, unique: true, index: true # this works well when the parent object is not optional (a pure belongs_to), and the parent can have only one child.
t.integer :some_count, null: false, default: 0 # all number fields should disallow nulls and have a sensible default value!
t.text :some_text, null: false, default: ""
t.text :email, null: false, unique: true, index: true
```

#### 2. Use Application-level validations on foreign keys
This mirrors the references field in DB-level validation, yes, but serves to provide error messages.

```rb
validates :user_id,
  presence: true
```

#### 3. Use Application-level validations on associations
This may seem redundant when the `user_id` foreign key already gets validated, but will save you when record's `:user_id` is present, but the associated object has vanished.  

```rb
validates :user,
  presence: true
```

![valid]({{ site.baseurl }}/images/validate/valid.png)
