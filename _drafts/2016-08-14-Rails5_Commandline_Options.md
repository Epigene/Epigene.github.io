---
layout: post
title: (Rails5) Commandline Options
---
Recently I wanted to set up a quick Rails test app, to show people how to get from zero to the Rails' "Hello World" view, and how adding new content works.  

I had trouble setting up Postgres, so I ended up generating an app without ActiveRecord, avoiding the database entirely. This was achieved by simply running `rails new . -O`.

Here's a review of command line options available in Rails5 and what they could be good for.

## Show Me

```
➜ rails -v
Rails 5.0.0.1
➜ rails
Usage:
  rails new APP_PATH [options]

Options:
  -r, [--ruby=PATH]                                      # Path to the Ruby binary of your choice
                                                         # Default: /home/augusts/.rvm/rubies/ruby-2.3.1/bin/ruby
  -m, [--template=TEMPLATE]                              # Path to some application template (can be a filesystem path or URL)
  -d, [--database=DATABASE]                              # Preconfigure for selected database (options: mysql/oracle/postgresql/sqlite3/frontbase/ibm_db/sqlserver/jdbcmysql/jdbcsqlite3/jdbcpostgresql/jdbc)
                                                         # Default: sqlite3
  -j, [--javascript=JAVASCRIPT]                          # Preconfigure for selected JavaScript library
                                                         # Default: jquery
      [--skip-gemfile], [--no-skip-gemfile]              # Don't create a Gemfile
  -B, [--skip-bundle], [--no-skip-bundle]                # Don't run bundle install
  -G, [--skip-git], [--no-skip-git]                      # Skip .gitignore file
      [--skip-keeps], [--no-skip-keeps]                  # Skip source control .keep files
  -M, [--skip-action-mailer], [--no-skip-action-mailer]  # Skip Action Mailer files
  -O, [--skip-active-record], [--no-skip-active-record]  # Skip Active Record files
  -P, [--skip-puma], [--no-skip-puma]                    # Skip Puma related files
  -C, [--skip-action-cable], [--no-skip-action-cable]    # Skip Action Cable files
  -S, [--skip-sprockets], [--no-skip-sprockets]          # Skip Sprockets files
      [--skip-spring], [--no-skip-spring]                # Don't install Spring application preloader
      [--skip-listen], [--no-skip-listen]                # Don't generate configuration that depends on the listen gem
  -J, [--skip-javascript], [--no-skip-javascript]        # Skip JavaScript files
      [--skip-turbolinks], [--no-skip-turbolinks]        # Skip turbolinks gem
  -T, [--skip-test], [--no-skip-test]                    # Skip test files
      [--dev], [--no-dev]                                # Setup the application with Gemfile pointing to your Rails checkout
      [--edge], [--no-edge]                              # Setup the application with Gemfile pointing to Rails repository
      [--rc=RC]                                          # Path to file containing extra configuration options for rails command
      [--no-rc], [--no-no-rc]                            # Skip loading of extra configuration options from .railsrc file
      [--api], [--no-api]                                # Preconfigure smaller stack for API only apps

Runtime options:
  -f, [--force]                    # Overwrite files that already exist
  -p, [--pretend], [--no-pretend]  # Run but do not make any changes
  -q, [--quiet], [--no-quiet]      # Suppress status output
  -s, [--skip], [--no-skip]        # Skip files that already exist

Rails options:
  -h, [--help], [--no-help]        # Show this help message and quit
  -v, [--version], [--no-version]  # Show Rails version number and quit

Description:
    The 'rails new' command creates a new Rails application with a default
    directory structure and configuration at the path you specify.

    You can specify extra command-line arguments to be used every time
    'rails new' runs in the .railsrc configuration file in your home directory.

    Note that the arguments specified in the .railsrc file don't affect the
    defaults values shown above in this help message.

Example:
    rails new ~/Code/Ruby/weblog

    This generates a skeletal Rails installation in ~/Code/Ruby/weblog
```

We will be focusing on the "Options" part.

## Discussion
#### `-d, [--database=DATABASE]`
One of core options. I hear `--database=postgresql` is popular.

#### `-B, [--skip-bundle]`
One of core options. Makes generation so much quicker.

#### `[--skip-keeps]`
I've not used this yet, but those .keep files annoy me, could be good.

#### `-M, [--skip-action-mailer]`
A good skip if the app will not be handling emails all that much.  

#### `-O, [--skip-active-record]`
Tricky. Normally you definitely want ActiveRecord present in the app to have models and communicate with the database.  
This can be useful if you use an ORM different from ActiveRecord, or if the app will not be storing anything in a (relational) database.

#### `-P, [--skip-puma]`
Useful if the app will not be using puma as the webserver.  

#### `-C, [--skip-action-cable]`
Useful if the app will not be doing realtime websockets action.

#### `[--skip-turbolinks]`
Turbolinks are a mixed blessing, I tend not to use them.

#### `-T, [--skip-test]`
For us RSpec users this reduces clutter.

#### `[--api]`
A new feature, configures app more suited for viewless functioning.  

## Examples

#### Opinionated Standart

```
$ rails new . -BMCT --database=postgresql --skip-keeps --skip-turbolinks
```

#### API app

```
$ rails new . -BMCT --database=postgresql --api --skip-keeps --skip-turbolinks
```

#### Using ActionCable and Turbolinks

```
$ rails new . -BMT --database=postgresql --skip-keeps
```

#### Not using Puma

```
$ rails new . -BCPMT --database=postgresql --skip-keeps --skip-turbolinks
```

![command-line]({{ site.baseurl }}/images/command-line/command-line.png)
