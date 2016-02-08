---
layout: post
title: Have Puma Boot With Server
---
[Puma](http://puma.io/) has been the de-facto recommended web server for Rails since 2015.  
It provides considerable memory savings over Unicorn and can be ran with a combination of settings for "workers" and "threads" to give your app the flexibility it needs in regards to resource managament and responsiveness.  
Unfortunately, tool ecosystem around Puma leaves much to be desired, specifically, it lacks a convenient way to be booted alongside the underlying Linux server.  
Here is our attempt at making it work __behind Nginx on Ubuntu with RVM__.

### 1. This assumes you can get Puma to work on production server when booted by hand

```ruby
# in /config/puma.rb

app = "my_app" # App-specific
root = "/home/deployer/apps/#{app}"

workers  6
threads  1, 1

rackup      DefaultRackup
environment "production"
daemonize   true

pidfile "#{root}/shared/tmp/pids/puma.pid"
stdout_redirect "#{root}/shared/log/production.log", "#{root}/shared/log/production_errors.log"
bind "unix:/tmp/puma.socket"
```

```
# in production server project /current directory
# given RVM bundled environment
$ bundle exec pumactl start #=> boots daemonized puma server
```

### 2. Make a RVM wrapper script to be used in init.d script
If this gets confusing, consult [RVM's own documentation on this](https://rvm.io/deployment/init-d).

Make the "wrapper"
```
$ rvm gemdir #=> get current gemset
$ rvm alias create <app_name> <app_gemset>
# e.g. rvm alias create my_app ruby-2.3.0@my_app_gemset
```

Find the wrapper's path and build a command
```
$ echo $rvm_path/wrappers/<app_name>
e.g. echo $rvm_path/wrappers/my_app

# say you want to see if `ruby -v` works via wrapper
<wrapper_path>/ruby -v

e.g. /home/deployer/.rvm/wrappers/my_app/ruby -v
```

### 3. Set up the init.d script
Open an editor for the script file

```
$ sudo nano /etc/init.d/<script_file>
e.g. sudo nano /etc/init.d/puma.sh
```

Fill the file with these two lines
```
#!/bin/bash
su - "<user>" -c "cd <app_current_directory> && <wrapped_command>"

# e.g. su - "deployer" -c "cd /home/deployer/apps/my_app/current && /home/deployer/.rvm/wrappers/my_app/bundle exec pumactl start"
```

Make the script executable
```
sudo chmod +x /etc/init.d/puma.sh
```

Generate the needed symbolic links
```
sudo update-rc.d puma.sh defaults
```

### 4. Troubleshooting
If you mess up init.d startup script linking, the links can be reset using
```
$ sudo update-rc.d -f <script_file> remove
e.g. sudo update-rc.d -f puma.sh remove
```

![puma]({{ site.baseurl }}/images/puma.png)
