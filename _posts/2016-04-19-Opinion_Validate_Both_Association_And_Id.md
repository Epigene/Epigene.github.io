---
layout: post
title: Opinion: Validate Both Association And Id
---
I will be mirroring a notion expressed [here](http://naildrivin5.com/blog/2015/11/15/rails-validations-vs-postgres-check-constraints.html), namely that

> Think about Rails validations as purely something for user experience.
> Think about check constraints as just being about data integrity.

Also, I will be criticizing a recommended `belongs_to` validation strategy espoused in [this article](http://railsguides.net/belongs-to-and-presence-validation-rule1/)

I strongly disagree with:
> Use association name for presence validator. (as opposed to :association_id)

My recommendation is threefold:

#### 1. Use Database-level constraints to ensure data integrity
This is simple redundancy that will spare you a lot of headache

#### 2. Use Application-level validations on associations to ensure data integrity


#### 3. Use Application-level validations on foreign keys to ensure data integrity

To me it is essential that I never get database errors. In my opinion, correct application code should handle 99.99% of validation errors.  

With these three layers in place your app will be nigh-impenetrable to bad data while providing error ques transparent to both users and developers.  

![puma]({{ site.baseurl }}/images/puma.png)
