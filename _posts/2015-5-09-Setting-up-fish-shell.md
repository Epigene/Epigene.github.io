---
layout: post
title: Setting up FISH shell
---
0. set up sublime for terminal:

```
$ echo "export PATH=~/bin:$PATH" >> ~/.profile
$ ln -s "/Applications/Sublime Text 2.app/Contents/SharedSupport/bin/subl" ~/bin/subl
$ subl PATH_TO_FILE
```

1. `$ brew install fish`
2. `$ curl -L https://github.com/bpinto/oh-my-fish/raw/master/tools/install.fish | fish`
3. add https://gist.github.com/Epigene/9bccb811c8b15524cfbc contents to ~/.config/fish/config.fish
4. `$ sudo nano /etc/shells`    # and add "/usr/local/bin/fish" bin path for fish
5. set to default:
  `$ grep -q '^/usr/local/bin/fish$' /etc/shells; or echo '/usr/local/bin/fish' | sudo tee -a /etc/shells`
  `$ chsh -s /usr/local/bin/fish`

![fish.png]({{ site.baseurl }}/images/fish.png)
