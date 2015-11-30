---
layout: post
title: Initialize Rails App
---
```
# Replace <project> with _myapp_ and you are good to go.

# Make new gemset of latest ruby found at https://www.ruby-lang.org/en/downloads/
$ rvm install 2.2.2
$ rvm use 2.2.2@<project> --create
$ gem install bundler
$ gem install rails

# Init rails app
$ mkdir <project>
$ cd <project>
$ git init
$ rails new . --git --database=postgresql --skip-bundle --skip-test-unit
```
