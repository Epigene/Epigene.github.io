---
layout: post
title: Setting up FISH shell on Mac OSX
---
Use homebrew to install fish v2+.  
Use Oh-My-Fish (get via curl) to make it pretty.  

```
$ brew install fish
$ curl -L https://github.com/bpinto/oh-my-fish/raw/master/tools/install.fish | fish

# add https://gist.github.com/Epigene/9bccb811c8b15524cfbc contents to ~/.config/fish/config.fish

$ sudo nano /etc/shells   # and add "/usr/local/bin/fish" bin path for fish

$ chsh -s /usr/local/bin/fish
```

![fish.png]({{ site.baseurl }}/images/fish.png)
