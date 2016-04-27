---
layout: post
title: Pattern: Asynchronize With Job
---
Sometimes Ruby code takes too long for web standarts, or there is some heavy processing.  
A pattern I use (and have trouble remembering) is to write code in a modular way.  
Consider this:

```ruby
class User < ActiveRecord::Base  

  def self.make_everyone_work_out
    User.all do |user|
      user.work_out
    end
  end  

  def work_out
    warm_up!
    run!    
    pump_iron!

    return true
  end
end
```

Now calling `User.make_everyone_work_out` will make you wait till everyone is done exercising, and who has the time for that?!  

You can refactor by identifying (or refactoring out!) the method that does the most work (pun not intended) and takes the longest.  
In this example the culprit is the `work_out` method.  
We need to make a job to delegate this method to.  

### 1. Start by defining a job
```ruby
# in /app/jobs/user_workout_job.rb

module UserWorkoutJob
  @queue = :user_workout_job # can be anything, but keeping naming consistent is easier to follow

  # allows convenient enqueuement
  def self.queue!(user_id:)
    return Resque.enqueue(self, user_id: user_id)
  end

  # must be defined to actually work via Resque or other adapter
  def self.perform(user_id:)
    # check out gem 'retryable'
    Retryable.retryable(tries: 3, on: JOB_ERRORS, sleep: lambda {|n| 4**n }) do      
      user = User.find(user_id)
      user.work_out

      puts "Job made user ##{user_id} work out!"
    end
  end

end
```

There are several gotchas, like jobs not liking ActiveRecord objects as arguments, since they are costly to serialize.  
To avoid this problem, pass simple objects like numerics and strings as arguments.  
in this instance, `:user_id` is passed as argument to job and `user` object is reloaded from the id.  

All a clean job should do is load an object or two and call the time-consuming method on it, which is what we have now.  

### 2. Replace method invocations with Job invocations

Going back, there was a class method

```ruby
def self.make_everyone_work_out
  User.all do |user|
    user.work_out
  end
end
```

Replace the costly `work_out` invocation with Job

```ruby
def self.make_everyone_work_out
  User.all do |user|
    UserWorkoutJob.queue!(user_id: user.id)
    puts "Enqueued UserWorkoutJob for User ##{user.id}"
  end
end
```

### 3. See how it works

Naturally, for jobs to work you will need a job handler like Sidekiq or Resque set up for your app, but that is a topic for another post.  
Assuming you can run jobs, to test in development you need to open the console in one terminal tab, and run the do-all development worker via `rake resque:work QUEUE='*'`
Call `User.make_everyone_work_out` and you should see console output telling you that jobs were enqueued, and in worker tab you should see output about them being performed.


Hooray, no more synchronious holdups!

![job]({{ site.baseurl }}/images/job.jpg)
