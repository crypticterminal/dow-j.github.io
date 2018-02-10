---
title: Setting Up a New Mac for Software Development
date: '2017-11-25 13:28:00 +0000'
author: dow-j
toc: true
categories:
  - software-development
published: true
---

* TOC
{:toc}
{: .markdown-toc}

Hi everyone and welcome to my site! This is my first blog post so please bear with me while I get the hang of things. If you'd like to learn a little bit more about me be sure to check out my [about me](https://jdow.io/about/) page.

<!--more-->

I recently purchased a new MacBook Air and I figured that I would document the steps I took to get everything up and running.

So without further ado, here are the steps I took to get it setup.

## Install Homebrew

[Homebrew](http://www.brew.sh) is a package manager for MacOS and was one of things I was most excited to try out with my new Mac. We will be using this along with Cask (more on that below) to get all of our software installed.

To install Homebrew open up terminal and paste/type in the following.

{% highlight shell %}
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

Verify your install by typing in `brew -v` to give you the version of Homebrew you installed. At the time of posting the latest version is `1.3.8`.

## Install mas-cli

mas-cli stands for Mac App Store Command Line Interface and we are going to use it to automate the installation of software from the App Store. To install it type the following into your terminal.

{% highlight shell %}
brew install mas
{% endhighlight %}

Then you want to sign into the the App Store if you haven't done so already during your inital setup.

{% highlight shell %}
mas signin youremail@example.com
{% endhighlight %}

After you sign in for the first time with mas-cli, or the regular app store you shouldn't have to sign back in again.

## Setting up a Brewfile

The beauty of HomeBrew isn't just that you can install applications right from your favorite shell, but that you can automate those installations using what's called a Brewfile.

To create one type the following into your terminal at your home directory.

{% highlight shell %}
touch Brewfile
{% endhighlight %}

The Brewfile lets us list all of the software we want and then install it in a bundle for the sake of convenience and automation.

Open up the Brewfile you just created with TextEdit, Nano, or any other text editor you have at your disposal for the moment (we will install of favorite momentarily) and paste in the below.

{% highlight shell %}
tap 'caskroom/cask'

brew 'git'
brew 'npm'
brew 'zsh'
brew 'z'

cask 'visual-studio-code'
cask 'flux'
cask 'firefox'
cask 'gimp'
cask 'google-chrome'
cask 'mamp'
cask 'spectacle'
cask 'vlc'
cask 'mysqlworkbench'

mas 'Numbers', id: 409203825
mas 'Pages', id: 409201541
mas 'Slack', id: 803453959
{% endhighlight %}

That looks like a lot to digest so lets go through line by line.

- The first thing we are going to tell Homebrew to install is [Cask](https://caskroom.github.io/) which extends Homebrew and lets you install virtually any software you want for Mac.

- Next up is [Git](https://git-scm.com/) which I use for version control across all of my projects, I would consider it industry standard at this point.

- [NPM](https://www.npmjs.com/) is the default package manager for Node.js and is almost universally used at the moment for sharing JavaScript packages.

- [zsh](http://www.zsh.org/) is an alternative to BASH and is the shell I plan on using, mainly to make use of the very cool [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) framework.

- [z](https://github.com/rupa/z) is a nifty little script that makes navigating your file system a little faster.

- [Visual Studio Code](https://code.visualstudio.com/) is my text editor of choice, I was a sublime text user for years but VSCode is crazy fast and the integrated terminal is fantastic.

- [f.lux](https://code.visualstudio.com/) dims your screen at night to make it easier on your eyes.

- [Firefox](https://www.mozilla.org/en-US/firefox/) second most popular browser at the moment, gotta cover your bases.

- [Gimp](https://gimp.org/) is an excellent alternative to photoshop.

- [Google Chrome](https://google.com/chrome) not much to say here, the most popular browser available at the moment with an excellent suite of dev-tools.

- [MAMP](https://www.mamp.info/) sandboxed PHP dev environment, mainly for WordPress.

- [Spectacle](https://www.spectacleapp.com) resize your windows!

- [VLC](https://www.videolan.org/vlc/index.html) a very solid media player that I've used for years.

- [mysqlworkbench](https://www.mysql.com/products/workbench/) connect and work with mysql DBs.

- [Numbers](https://www.apple.com/numbers/) alternative to Excel, comes installed with MacOS

- [Pages](https://www.apple.com/pages/) alternative to Word, comes installed with MacOS

- [Slack](https://slack.com/) used extensively by several times I've been a part of for chatting.

With all that out of the way, lets get all of these installed. Type the following into your terminal

{% highlight shell %}
brew bundle install
{% endhighlight %}

## Making Our Terminal Awesome

While BASH is a fantastic shell, lets take our terminal to the next level by getting zsh up and running using the oh-my-zsh framework.

### ITerm2
Firstly, lets get a new terminal installed. The one I'm going to use is [ITerm2](https://www.iterm2.com/). Navigate to that site, download ITerm2, and put the application into your Applications folder.

### Disable Last Login Message
You may have noticed the, somewhat annoying, `Last Login: #:##:##` message at the top of your terminal when you open it for the first time on a given day. In order to disable that all we need to do is open our terminal and type `touch ~/.hushlogin` the presence of this file in our home directory will prevent this message from appearing.

### ZSH
We installed ZSH when we ran our Brew Bundle Install earlier now we need to set it as our default shell. This is easy enough to accomplish and we can do so by typing the following into our terminal.

{% highlight shell %}
chsh -s /usr/local/bin/zsh
{% endhighlight %}

### oh-my-zsh
As I mentioned earlier, my favorite thing about zsh is the amazing [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) framework we can use alongside it. In order to install oh-my-zsh type the following into your terminal 

{% highlight shell %}
$ curl -L http://install.ohmyz.sh | sh
{% endhighlight %}

After running that command you should have a `.zshrc` file in your home directory. Open it up and you can change your theme. I personally like a solarized look and the [agnoster](https://github.com/agnoster/agnoster-zsh-theme) theme lends itself to this nicely.

{% highlight shell %}
ZSH_THEME="agnoster"
{% endhighlight %}

In order to get the theme completely setup you need to be using a font with ITerm2 that supports the characters it uses. I recommend [Fira Code](https://github.com/tonsky/FiraCode) for this.

Finally to complete the look set the color scheme for ITerm2 to `Solarized Dark` by going into `Preferences > Profiles > Colors > Color Presets`.

Reload your terminal or type `source ~/.zshrc` and you should be good to go, but there are still a couple more fun things we can do.

### zsh-syntax-highlighting
Okay so zsh syntax highlighting is really cool, once you have it setup it will tell you if a command is valid before you enter it. to install it type the following into your terminal

{% highlight shell %}
cd ~/.oh-my-zsh && git clone git://github.com/zsh-users/zsh-syntax-highlighting.git
{% endhighlight %}

then add this line to your `.zshrc` file

{% highlight shell %}
source ~/.oh-my-zsh/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
{% endhighlight %}

restart your terminal or run `source ~/.zshrc` and you're good to go.

### Z Script
Earlier we installed z script, so now lets get it running. All you need to do is add this line to your `~/.zshrc` file and hit save.

{% highlight shell %}
. `brew --prefix`/etc/profile.d/z.sh
{% endhighlight %}

It's really cool because it keeps track of the places you've cd'd into and stores them in a `~/.z` file so that you can easily navigate to them in the future. For example, instead of doing `cd ~/someplace/someproject/source` you can simply type `z source`.

## NPM
Typically you will use NPM on a per project basis, but I do use Gulp globally so let's install that now

{% highlight shell %}
npm install -g gulp
{% endhighlight %}

## Ruby
Ruby is required to run [Jekyll](https://jekyllrb.com/), which I use to power this blog. So lets get that installed.

### RVM
I'm using RVM to make sure I have the latest version of Ruby installed without messing with the builtin system Ruby. To install RVM type the following

{% highlight shell %}
\curl -sSL https://get.rvm.io | bash -s stable
{% endhighlight %}

### Install the Latest Version of Ruby
In order to install the latest version of Ruby type the following into your terminal

{% highlight shell %}
rvm install ruby-head
{% endhighlight %}

You can see what versions of Ruby you have installed by typing `rvm list`. We want to set our default version of Ruby to be the latest so do that by typing the following command

{% highlight shell %}
rvm --default use 2.4.1
{% endhighlight %}

in my case the latest version is `2.4.1` but at the time of reading this it could be a later version so make sure you use that one instead. You can confirm you're using the version you set by using the command

`rvm -v` it should print `2.4.1` in the console in my case, or whatever the version you entered in the prevous command is.

### Install Bundler

Gem is Ruby's package manager and we are going to use that to install bundler, which is another package manager that is required to use Jekyll. In order to install it type the following command

{% highlight shell %}
gem install bundler
{% endhighlight %}

### Install Jekyll

Finally we can install Jekyll, the static site generator. In order to install it type the following command into your terminal

{% highlight shell %}
gem install jekyll
{% endhighlight %}

Once it's installed confirm it's working by entering the following commands

Create a test site

{% highlight shell %}
jekyll new test-site
{% endhighlight %}

CD into it

{% highlight shell %}
cd test-site
{% endhighlight %}

Serve it up

{% highlight shell %}
bundle exec jekyll serve
{% endhighlight %}

Browse to http://localhost:4000 and you should see your test site up and running. You can hit `ctrl + c` in your terminal to stop running the site and then remove the directory with `rm -rf test-site` since it was only for testing.

## Preferences

Before getting started I found setting these preferences helpful.

- Keyboard > Text > Disable "Correct Spelling Automatically"
- Security and Privacy > FileVault > On (Encrypt your drive)
- Security and Privacy > Firewall > On (who doesn't like a little extra security?)
- Security and Privacy > General > App Store and Identified Developers
- File Sharing > Off
- Users & Groups > Login Items > Spectacle, Flux (I like these on at startup)

Finally some commands to make your life easier

Show Library Directory

{% highlight shell %}
chflags nohidden ~/Library
{% endhighlight %}

Show Hidden Files

{% highlight shell %}
defaults write com.apple.finder AppleShowAllFiles YES
{% endhighlight %}

Show Path Bar

{% highlight shell %}
defaults write com.apple.finder ShowPathbar -bool true
{% endhighlight %}

Show Status Bar

{% highlight shell %}
defaults write com.apple.finder ShowStatusBar -bool true
{% endhighlight %}

## Conclusion

There are still a lot of things to setup, mainly SSH and Virtual Machines. But for now this is all I need to be up and running. I plan on putting out more tutorial-esq posts like this in the future so if this helped you at all please let me know!
