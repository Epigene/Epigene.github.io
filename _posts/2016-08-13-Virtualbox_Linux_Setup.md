---
layout: post
title: Host Virtualbox Linux on Windows machine for Rails Development
---
Developing Rails apps requires a \*nix platform, because Windows' terminal leaves much to be desired.  
Macs tend to be expensive, especially for tinkering around.  
Enter Linux in Virtualbox!  

#### Pros
1. Free! Turn that Windows gaming rig into a respectable development workstation with open source tools.

#### Cons
1. You will be using Linux, you better know what you are doing...
2. Can be a pain to set up.  

#### Difficulty and Time
Difficulty: Medium - lots of tools and environments involved, but mainly configuration problemsolving.  
Time: A good long afternoon.

## 1. The Windows Host
I had a Windows7 with 8gb RAM and spare 12GB of disk space.  
Dedicated 5gb of the ram to the virtual Linux and all works well.  

#### 1-1 Install Virtualbox  
Head over to [Oracle homepage](https://www.virtualbox.org/).  
Grab and install:  

1. VirtualBox <version> for Windows hosts
2. The extension pack  

#### 1-2 Get a Linux Distro
I went with [Lubuntu](http://lubuntu.net/).

#### 1-3 Create a Virtual Machine
Look up how to do it.  
Make sure you mount the Linux distro image before booting it.  

#### 1-4 Install Linux
After boot you should be prompted by Linux installer.  
Have it finish successfully.  
Unmount the install image after setup and reboot the VM.  

#### 1-5 Install Guest Additions
Open the Terminal with Ctrl+Alt+T  

```
sudo apt-get install virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11
```

Reboot VM, increase resolution to native and go Fullscreen mode with RCtrl+F

## 2. The Linux Guest

### 2-0 Library Setup
Vanilla Lubuntu is missing several key libs web developers will need, run this command

```
sudo apt-get install -y fish curl git tilda fonts-dejavu nodejs libxslt-dev libxml2-dev
```

### 2-1 Terminal and Shell
I use Fish shell.  
Tilda allows for nice customization.

#### 2-1-1 Fish Setup

Install Fish shell from official repo ([original thread](https://hackercodex.com/guide/install-fish-shell-mac-ubuntu/))

```
sudo apt-add-repository ppa:fish-shell/release-2
sudo apt-get update
sudo apt-get install fish

# Make Fish your default shell:
chsh -s /usr/bin/fish
```

Install Oh-My-Fish

```
$ curl -L http://get.oh-my.fish | fish
```

Set Fish as default login shell

```
# get where fish installed
$ which fish

# add fish to shells
$ echo "<fish path>" | sudo tee -a /etc/shells

# make fish the default shell
$ chsh -s <fish path>
```

Set theme and some aliases

```
# theme
$ omf install robbyrussell
$ omf theme robbyrussell

# nano the fish config
$ nano ~/.config/fish/config.fish

    alias opsh="sudo nano /home/augusts/.config/fish/config.fish "
    alias ls="command ls -A --color --group-directories-first"

    set -g -x fish_greeting ''
    echo -e "Proceed to \033[38;5;208mfishy\033[0;00m business!"
```

#### 2-1-2 Tilda setup

```
$ sudo apt-get install tilda fonts-dejavu

$ tilda
```

Configure:

1. Where you want the tilda window to be
2. Use of 12pt DejaVU mono Book fonts-dejavu
3. Use of initial command to run fish

![terminal look]({{ site.baseurl }}/images/virtualbox/terminal.png)

### Misc Packages

```
sudo apt-get install nodejs libxslt-dev libxml2-dev
```

### Keys
You want to map CapsLock key to act as Return.  
You might also want to remap other keys.

Start by consulting the [in-depth thread](http://askubuntu.com/questions/254424/how-can-i-change-what-keys-on-my-keyboard-do-how-can-i-create-custom-keyboard/257497#257497).  
Here's a key symbol [reference](http://wiki.linuxquestions.org/wiki/List_of_Keysyms_Recognised_by_Xmodmap).  

Place the remaps somewhere they will be loaded very late, like fish.conf  

__NB__, remaps, especially Capslock -> Return will break on relogging, just rerun the command.  

```
xmodmap -e "keycode 49 = Delete Delete"
xmodmap -e "keycode 119 = grave asciitilde"
xmodmap -e "keycode 66 = Return Return Return" -e 'clear Lock'
```

#### Examples

##### Tilde/grave to Del

```
# 1. run `xev` command and get keycodes and event names for Delete, grave, tilde (and some possible Alt-symbols)

# 2. run `xmodmap` to change tilde to del and del to tilde
# keycode 119 is Delete by default
# keycode 49 is grave by default
xmodmap -e "keycode 49 = Delete Delete"
xmodmap -e "keycode 119 = grave asciitilde"
```

##### Caps to Enter

```
xmodmap -e "keycode 66 = Return" -e 'clear Lock'
```

### RVM

```
# add gpg key
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

# Install rvm, ignore the warnings about fish
$ curl -sSL https://get.rvm.io | bash -s stable --ruby

# Download fish functions
curl -L --create-dirs -o ~/.config/fish/functions/rvm.fish https://raw.github.com/lunks/fish-nuggets/master/functions/rvm.fish

# Activate them
$ echo "rvm default" >> ~/.config/fish/config.fish

# Verify
$ rvm --version

# Disable gem documentation
# Make a file ~/.gemrc and put this in it:
$ gem: --no-rdoc --no-ri 
```

### Postgres

Install the packages

```
$ sudo apt-get install postgresql-common postgresql postgresql-contrib libpq-dev python-dev
```

```
$ sudo -u postgres createuser <your username>

# If you would like to set a password for the user, you can do the following
sudo -u postgres psql postgres

ALTER USER <your username> PASSWORD '<pass>';
ALTER USER <your username> CREATEDB;
ALTER USER <your username> WITH SUPERUSER;
\q
```
### MySQL
See this [DO post](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-14-04)

### Redis

#### Install deps

```
sudo apt-get update
```

```
sudo apt-get install -y build-essential tcl8.5
```

#### Get code and install

```
wget http://download.redis.io/releases/redis-stable.tar.gz

tar xzf redis-stable.tar.gz

cd redis-stable
make
sudo make install
```

#### Setup deamon

```
cd utils
sudo ./install_server.sh # the default 6379 port is fine for development
```

#### Test

```
redis-cli

> ping
```

Blatantly stolen from [my gist](https://gist.github.com/Epigene/f5e63e30ab7f9680e5f4).  

### Development DNS

Accessing local webapps via localhost or IP is cumbersome and does not represent production environment.

We will set up `*.dev` TLD for development needs.

This will be a two-step process.  
First we will muck with hosts to redirect all requests to .dev to the localhost 127.0.0.1 IP address.  
Second, we will reconfigure the IP table to redirect requests to 127.0.0.1:80 to 127.0.0.1:3000.  
This will produce the effect that a server started with `rails s -b 127.0.0.1 -p 3000` will be accessible at `http://<anything>.dev`

#### Change Hosts

Consult [this discussion](http://askubuntu.com/a/233209/254615) for insight.

#### Change IP table

```
sudo iptables -t nat -A OUTPUT -p tcp --dport 80 -d 127.0.0.1 -j DNAT --to-destination 127.0.0.1:3000
```

See [this discussion](http://serverfault.com/a/54371/294240) for insight.

## 3. Tools

### 3-1 The Editor
Go with what you like and are familiar with.  
I like and am familiar with GitHub's [Atom](https://atom.io/).

## 4. First App
By now it should be possible to get to Rails' "Hello World" screen of a new app.  

#### 4-1 Set Up Ruby

```
$ rvm install ruby-2.3.1

$ rvm use 2.3.1@first-app --create

$ gem install bundler

$ gem install rails
```

#### 4-2 Set Up a Rails Project With Git

```
$ mkdir ~/Projects/first-app

$ cd ~/Projects/first-app

$ git init

$ rails new . -BMCT --database=postgresql --skip-keeps --skip-turbolinks
```

#### 4-3 Configure DB

```
# in /config/database.yml

default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  database: first-app

development:
  <<: *default

test:
  <<: *default
  database: first-app_test

production:
  <<: *default
```

```
$ rails db:create db:migrate
```

#### Run The Server

```
$ rails s -b 127.0.0.1 -p 3000
```

The app should be reachable at http://local.dev

Yayifications!

## Troubleshooting

### My VM disc is too small!
Resize following [this video](https://www.youtube.com/watch?v=ikSIDI535L0)
Then fix the swap fstab by:

```
# 1. Output available partitions
$ sudo blkid

# 2. Edit fstab and change swap UUID to correct one
$ sudo -H nano /etc/fstab
```
