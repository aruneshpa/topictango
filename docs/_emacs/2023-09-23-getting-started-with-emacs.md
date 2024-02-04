---
layout: single
title:  "Getting Started with Emacs"
excerpt: "Emacs 101.  Downloading, installing, and some key concepts"
categories: 
 - emacs
tags:
 - emacs
date:   2023-09-20 10:58:01 -0700
categories: emacs
author: Arunesh Pandey
---

# Download and Install

Surprise Surprise!! To be able to use Emacs, you need to install it. Just like most tools out in the wild, there are a few ways to install Emacs. I will list these out in the increasing order of difficulty.

## Using the builtin package manager of your Operating System

This is the simplest way to install Emacs. You can use the package manager for your Operating System. For Mac OS, this can be brew or pacman, for debian flavors of Linux (e.g. Ubuntu) you can use apt, for Red Hat and other similar flavors, you can use yum and so on. One disadvantage of this method is that you can only install the version of Emacs that is available in your package manager's repository. However, usually there are ways around that (typically by using a custom repository).

## Installing from tarball

You can also download the released tarballs directly and install Emacs. The advantage of this method is that you have slightly more control. You can download the tarball of the Emacs version that you want to install. You can use one of the FTP mirrors to download the tarball. Once you have the tarball, you can build and install Emacs by running the following commands (similar to most GNU software):

``` shell
./configure
make
make install
```

The configure step is where we specify new flags. For example, to compile Emacs with GTK widgets, we can use ./configure --with-xwidgets.

## Building from source

This is the hardest but most customizable way to install Emacs. You can clone the Emacs source and build it locally. This is helpful if you want to try the most latest and greatest top-of-tree Emacs features. You can clone the Emacs repository and build Emacs on the the master branch. All new major features get their own feature branch before eventually getting merged in master. So, if you want to try a shiny new feature (e.g. native compilation), you can do that by building Emacs from the feature's branch.

To build Emacs on master branch:

``` shell
git clone https://github.com/emacs-mirror/emacs.git
git checkout master
./configure # with custom features that you want to enable
make
make install
```

# Ready to Go!!

Once you install Emacs, you should be ready to go. Try opening a file to test your installation (use the key binding C-x C-f). Try to navigate in the file using the C-n, C-p, C-f and C-b bindings.

# Notes

## Elisp (Short for Emacs Lisp)

Most of the Emacs's source code is in two languages: C and Elisp. Unless you are digging really deep into the source code of Emacs to fix a bug, you can ignore the C bits. For everything else- including the "config file" (the file used to configure Emacs instance), Emacs uses Elisp. For this reason, I highly recommend some familiarity with Elisp. Luckily, Emacs has an inbuilt manual for learning Elisp (C-h i m Emacs Lisp Intro).

## Need Help?

Before we start, if you are stuck at any point during your configuration use the Emacs's builtin documentation. You can refer to the following table to get help of a specific type:

| Key Binding | Description                                                 |
|:------------|:------------------------------------------------------------|
| C-h v       | v for variable. Describe a variable                         |
| C-h f       | f for function. Describe a function                         |
| C-h a       | a for apropos. Describe something, I am not sure what it is |
| C-h k       | k for key binding. Describe a key binding                   |
| C-h C-h     | h for help. Help on help                                    |


As you can see, there is a pattern here. C-h is the general help prefix, followed by the command that you want help on. Don't worry if the key bindings are getting hard to remember. In future posts, we will use packages that make learning and discovering new bindings easier.

# Whats Next?

In coming posts, we will modify our Emacs config to give our Emacs installation a more modern feel, before unleashing the full customization potential of Emacs and making it a fully functional IDE (Integrated Development Environment).
