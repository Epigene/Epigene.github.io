---
layout: post
title: (Rails) Initialize App in 2018
---

```
# Replace <project> with _myapp_ and you are good to go.

# Make new gemset of latest ruby found at https://www.ruby-lang.org/en/downloads/
$ rvm install 2.5.0
$ rvm use 2.5.0@<project> --create
$ gem install bundler
$ gem install rails -v 5.2.0.beta2

# Init rails app
$ mkdir <project>
$ cd <project>
$ git init
$ rails new . -BMCT --git --database=postgresql --skip-keeps --skip-turbolinks
```
