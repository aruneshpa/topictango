---
layout: single
title:  "Beginners Emacs Configuration: Getting Started"
excerpt: "In this post, we will come up with a minimal working Emacs configuration"
date:   2023-09-20 10:58:01 -0700
categories: 
 - emacs
tags:
 - emacs
author: Arunesh Pandey
permalink: /emacs/beginners-config/

---

In the last post, we talked about installing Emacs. As you might have guessed from the title, in this post we are going to come up with a minimal configuration that makes Emacs functional and visibly pleasing. Don't get me wrong, you can use Emacs out of the box, but the customization presented here will make the experience better.

# More Terminology

## Package Repositories

Text editors are generally designed to do only that, "edit text". However, editors also have hooks where developers (and sometimes companies) can write plugins/packages to achieve certain functionality. For example, consider a package that shows a vertical line on your screen at certain column width so that developers can easily know the width of their current line of code. This was (and in some cases, still is), used by the linux kernel development teams to limit the line width to 80 characters. Anyway, we need a repository for these packages so that Emacs can query them from a central repository if a user wants to install a package. That is what a package repository means in Emacs terms.

There are a few different types of package repositories in Emacs:

- GNU: GNU packages are part of this repository.
- ELPA: Emacs Lisp Package Archive. The packages that are part of Emacs go here.
- MELPA: Milkypostman’s Emacs Lisp Package Archive). Packages that are maintained by external developers.

It helps to make sense of these repositories if you think of these in reverse. If you were a new developer and wanted to write a new package, you would release it on MELPA. Then, if the Emacs maintainers find that your package is really useful and it should be part of Emacs (and it conforms to the OSF principles), then it will be promoted to ELPA. Also note that there is a *-stable version of these repos as well (e.g., melpa-stable). Org mode (more on that in a later post), uses a separate package repository as well.

# Major and Minor Modes

Emacs has a concept of modes which represent how Emacs treats a particular buffer. A major mode represents the "principle" mode of the buffer, and a minor mode represents the "helper" mode that can augment the major mode. A buffer can only be associated with one major mode, but several minor modes. An example would be, if you are browsing a C++ file, your major mode will be cpp-mode. On top of this, you might have minor modes enabled to automatically insert a closing brace if an opening brace is inserted (smartparent-mode), and a minor mode to show you the 80 character vertical line (display-fill-column-indicator-mode).

# Fun Stuff

## Package Repositories

Alright, let's start with the fun stuff. First, we need to configure package repositories so Emacs can pull in packages. From Emacs 27 onward, we can do that as part of early initialization (a file named \~/.config.early-init.el).

``` emacs-lisp
(require 'package)
(setq package-archives
      '(("melpa" . "https://melpa.org/packages/")
        ("elpa" . "https://elpa.gnu.org/packages/")
        ("gnu" . "https://elpa.gnu.org/packages/")))
```

Pro tip: If we want, we can also assign priorities to the package repositories so that one package gets precedence over another in case it is available in more than one repositories. For example, if you prefer installing the stable version of packages, you can assign higher priorities to the *-stable repositories.

## Configure use-package

Next, we are going to use use-package in our init file to load and initialize packages. This is a very handy tool which makes loading and customizing packages very easy. Add the following snippet in your init file to configure use-package to so we can use it to install and configure other packages.

``` emacs-lisp
(unless (package-installed-p 'use-package)
  (package-install 'use-package))
(require 'use-package)
; ensures that each package that is configured using ~use-package~ is installed
(setq use-package-always-ensure t) 
```

## Basic Visual Configuration

I usually like to set a few sane defaults at the top of my init file. Usually these are visual elements like width of the tab character, showing time in the Modeline (the line at the bottom of your Emacs buffer). I also like to use this section to clean out some cruft that I do not want (e.g., don't show scratch buffer message, hide scroll bar etc). The following section lists the defaults that I set, along with a brief explanation of each configuration.

``` emacs-lisp
(setq-default
  ; Prefers spaces over tabs, tabs are evil
  indent-tabs-mode nil
  ; Set width for tabs
  tab-width 4
  ; Prefers the newest version of a file
  load-prefer-newer t
  ; Set the full name of the user
  user-full-name "xxxx xxxx"
  ; Set the email address of the user
  user-mail-address "abcxyz@gmail.com"
  ; The confirmation string when exiting Emacs
  confirm-kill-emacs 'y-or-n-p)

;; Enable the following minor modes globally

; Show the column number
(column-number-mode 1)
; Display time in the mode-line
(display-time-mode 1)
; Replace yes/no prompts with y/n
(fset 'yes-or-no-p 'y-or-n-p)
; Highlight current line
(global-hl-line-mode)
; Show the matching set of parentheses
(show-paren-mode 1)
; Display battery percentage
(display-battery-mode 1)
```


This should give you a minimal, usable configuration that is free of clutter. If you want to further "trim down the excess fat" from your Emacs, I recommend adding the following to your init file:

``` emacs-lisp
; Disable start-up screen
(setq inhibit-startup-screen t)
; Empty the initial \*scratch\* buffer
(setq initial-scratch-message "")
; Hide the Menu bar
(menu-bar-mode -1)
; Hide the Scroll bar
(toggle-scroll-bar -1)
; Hide the tool bar
(tool-bar-mode -1)
```

That's it. This configuration should be enough to get you started with Emacs. Please note that most of the configuration here is option (e.g., hiding menu and scroll bars), so feel free to skip that. Play around with your config. Add one line of config at a time and reload Emacs (eval-buffer) to observe the effect of that config. 

Good luck with your exploration.
