---
layout: post
title:      "Setting Up My Local Environment"
date:       2018-05-27 17:20:21 +0000
permalink:  setting_up_my_local_environment
---


After submitting my Sinatra project, I decided it was time to switch from the Learn IDE to using a local environment. This idea of seeing up a local environment has been intimidating, but I decided today was the day to learn about it.

Luckily Flatiron school has a lot of resources that walk you through this process, such as [this link](http://help.learn.co/workflow-tips/local-environment/mac-osx-manual-environment-set-up). After what felt like an endless stream of downloads, I finally managed to get it up and running. I downloaded Atom to use as my text editor. It will definitely take some getting used to, but it makes me feel a little bit closer to becoming that cool coder woman of my dreams.

First I had to uninstall Learn IDE, by installing an app cleaning program--seems a bit counterintuitive to install something to uninstall something else, but hey--it worked! 

Then I installed Xcode Command Line Tools by typing `xcode-select --install` in terminal.

Next, I installed Homebrew by typing `ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` into terminal. Here, it asked me for a password and I had no administrator password set up on my computer (whoops!). So I had to create a password, run the command again, and enter the password I just created. Not so bad.

Next I created a new SSH key and added it to my github. [This link](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) was helpful.

I also ran `git config --global user.email "mallory.feuer@gmail.com"` and `git config --global user.name "MFeuer23"` so the git running on my machine will know who I am.

The next step was to add some support libraries by running `brew install gmp` and `brew install gnupg` in terminal. I'm not sure exactly what the purpose of support libraries are, so I did a little internet digging and found some resources that explain what [gmp](https://gmplib.org/) and [gnupg](https://www.gnupg.org/) are. 

Next was installing Ruby Version Manager, which allows us to switch between different versions of Ruby for different projects. More info about RVM [here](http://rvm.io/).

In what seemed like a whirlwind, I then installed some ruby gems, set up the Learn gem, downloaded Atom Text Editor, and set it to be my default text editor. Then installed sqlite and Postgres, rails, dotfiles, node, and Java Next.

Here I am ready to use my local environment (I hope). Thanks for reading!
