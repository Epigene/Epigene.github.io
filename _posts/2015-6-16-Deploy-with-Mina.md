---
layout: post
title: Deploy Rails applications with Mina
---
Modern deployments require finesse. Long gone are the days of FTP file upload to server.
These days we deploy version-controlled (GIT) code over SSH connections using automated systems. On Rails Capistrano is a widely used deployment system, however, we at Creative opted for an up-and-coming alternative that is easily extendable (basically Rake) and __real bloody fast__

##### Enter Mina
For Mina deployment you write what are essentially rake tasks which are then concatenated in one large Bash script automagically and executed over one SSH connection. THis strategy is what makes Mina so fast. It also comes with in-built checks for need to migrate and compile assets, making most deploys last ~20s since migrations and asset precompilation can be skipped.

##### What Creative Mina deploy does
###### Standart deploy

  1. Pulls code from a GIT repo (master) branch
  2. Links the code to shared folder (public assets), links nginx etc.
  3. __Conditionally__ migrates and precompiles
  4. Updates crontab
  5. Runs resque
  6. Phases puma server

###### Additional goodies

  1. In-built remote rake rask invocation with `mina rake[namescpace:task]`
  2. Rollback functionality
  3. Checks whether remote HEAD commit matches local one
  4. Control puma server and nginx
  5. Specify to which environment to deploy (production, staging)

##### The actual deploy.rb

```ruby
desc "Deploys the current version to the server."
task :deploy => :environment do |t, hash|

  if heads_match?
    deploy do
      invoke :'git:clone'
      invoke :'deploy:link_shared_paths'
      invoke :'bundle:install'
      invoke :'rails:db_migrate'
      # ...
```

##### Thanks!

[Full code in the gist.](https://gist.github.com/Epigene/3ec6635246be042fac90)

[Mina](http://nadarei.co/mina/) homepage.

![pdf.png]({{ site.baseurl }}/images/mina_logo.png)
