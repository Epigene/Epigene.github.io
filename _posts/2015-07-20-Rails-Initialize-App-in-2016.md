---
layout: post
title: (Rails) Initialize App in 2016
---

```
# Replace <project> with _myapp_ and you are good to go.

# Make new gemset of latest ruby found at https://www.ruby-lang.org/en/downloads/
$ rvm install 2.2.2
$ rvm use 2.2.2@<project> --create
$ gem install bundler
$ gem install rails -v 5.0.0.beta4

# Init rails app
$ mkdir <project>
$ cd <project>
$ git init
$ rails new . --git --database=postgresql --skip-bundle --skip-test-unit
```
