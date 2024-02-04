---
layout: single
title:  "Beginners Emacs Configuration: Getting Started"
excerpt: "In this post, we will configure Emacs as a fully functional Golang IDE"
date:   2023-09-20 10:58:01 -0700
categories: 
 - emacs
tags:
 - emacs
author: Arunesh Pandey
---

Hello and welcome to another post about Emacs. In this post, we will try to set up a functional development environment in Emacs with minimal configuration. I will explain each configuration option briefly as we go along, but I will also drop links to the detailed documentation so you can read it in detail. If you are new to Emacs, please check out the previous post on how to run Emacs with a basic configuration.

If you made it here, you are either a beginner who is slowly making progress, or, you are looking for an answer to a very specific issue that you are facing in your customization – maybe your code completions are not working. Don't worry, we will cover both in this post. In this post, we are going to set up a fully functional development environment for Golang (Go for short) based projects with features like completion, jump to definition/references/implementations, automated refactors, in built debugging etc. Buckle up!

# Why Emacs?
Okay, I know! If you are already here, then it is pretty sure that you want to try out Emacs. But still, I will give a few points that make Emacs a unique and better than other editors:

## Infinite Customization

This should not surprise you. Emacs offers you a level of customization that no other editor can. If you don't like the default completion framework in Emacs, you can swap that out with your preferred one. You don't like a searching framework, you can replace it. You want you documents to have a different font than your code, you can do that. We can go on and on. You can customize almost anything in Emacs.

## Hacking

This post is geared towards software engineers, so my views will be a little biased. Personally, I feel that "hacking" Emacs to make it to what you want is part of the fun. Sometimes it leads you to dark paths of spending hours to look through Elisp code to grok the implementation of a Elisp module that you want to use. Sometimes, you spend your entire weekend trying to configure email on your Emacs using Mu4e. But in return, you also get to learn fun things about operating systems and other software. It's fun.

# Terminology

Before we get started, let's define a few terms that we will use throughout this post. Beginner users who are still not used to Emacs terms can reference the following table:

| Term   | Meaning in Emacs World                                                                                                                                                  |
|:-------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Buffer | Essentially how Emacs interacts with a file or a process. Each open file will have it's open buffer. Processes usually output things to a buffer (which is not a file). |
| Window | A view into the file or the process. For example, if the buffer is for a file, you will see the contents of that file.                                                  |
| Frame  | A Window. When you open Emacs in GUI, that's an Emacs Frame. In a Frame, you can have many buffers. At a time, you can operate on one buffer in one window.             |
| Point  | Cursor.                                                                                                                                                                        |

# Components of an IDE

There are a few very important components that make an IDE.

## Language Support

The editor should be able to understand the programming language of the contents of the buffer. Ideally, it should also perform basic functions like syntax based highlighting.

## Completion Framework

The editor should be able to parse the code at point and suggest the possible completions. It is okay if the editor offloads this work to external tools/plugins. But they should integration seamlessly with Emacs and easily.

## Code Navigation

The editor should support easy and quick code navigation. We should be able to jump to definitions, references and implementations.

## Integration with Version Control Systems

We should be able to perform some basic version control tasks from within the editor (e.g., creating a branch, commit changes).

## Integration with a Debugger

We should be able to attach the debugger to a running process from within the editor to perform simple debugging steps like printing the stack trace.

## Support for Running Tests

The editor should understand and enable users to write tests easily and run them from within the editor. For example, this involves templates that instantiate easy test suites and tests.

Some of these components are aspirational (debugger support), but others are almost necessary for a productive development environment (e.g., jump to definition).

# Configuration

## Basic Configuration

Alright, on to the fun stuff now. First thing first, let's set up the language support in the Editor. In the previous post, we talked about how a "mode" is how Emacs handles a particular buffer. The mode for Golang is called "go-mode". Let's install go-mode:

``` shell
M-x package-install go-mode
```

Once go-mode is installed, Emacs will automatically start highlighting any new buffers with Golang code.

## Navigation and Code Completion

Historically, the package that handled the language (e.g., go-mode for Golang) was also responsible for implementing the completion and navigation support. This resulted was a lot of duplication – every editor had to implement their own version of parsing the syntax tree of the code and suggest completions. All this changed in 2015, when Microsoft launched Language Server Protocol. In this model, we have a language server (usually each language has one server) and clients (editors) get the recommendation from the server.

The Emacs package that interacts with the language server is lsp-mode. Installing and configuring it is really easy since I believe it is one of the most documented and actively developed features today. Please check out the official documentation of lsp-mode which describes the sample configuration for different languages.

So, to sum it up, this configuration has two components:

Server Side: A standalone binary that provides IDE features such as completion, navigation etc. to any LSP compatible editor. For Golang, the language server is called gopls (pronounced "Go Please"). Please ensure that gopls is installed and is available on your PATH.

Client Side: Emacs plugin (called lsp-mode) that connects with the language server to get suggestions for the buffer. This plugin can be customized to use different language servers for different languages. We can also start the language server with custom flags (e.g., to enable debug logging, to enable experimental features). 

``` emacs-lisp
(use-package lsp-mode
:commands (lsp lsp-deferred)
:init
(setq lsp-keymap-prefix "C-c l")
:config
(lsp-enable-which-key-integration t)
:hook
((go-mode) . lsp))
```

Let's decipher the above snippet.

- `lsp-deferred` allows Emacs to defer the loading of the lsp-mode until a .go file is opened.
- `lsp-keymap-prefix` allows you to set a custom prefix for lsp-mode. I like `C-C l` prefix (and it is a common, recommended one for lsp-mode).
- `lsp-enable-which-key-integration` integrates lsp-mode with which-key. which-key, gives you helpful mini-buffer suggestion for possible keybindings after you press a prefix key. For example, if you pause after `C-c l`, which-key suggests all possible completions with helper texts so you don't have to memorize the bindings.
- `Hook`: This line adds the lsp command hook in go-mode. Essentially, this means that whenever a go-mode buffer is opened, the lsp-mode package is loaded.

You can also enable the lsp-ui extension which adds various UI elements (e.g., doc frames, peeks). I personally find it slightly intrusive but your mileage may vary. Add the following snippet to enable lsp-ui.

``` emacs-lisp
(use-package lsp-ui
  :hook (lsp-mode . lsp-ui-mode)
  :config
  (setq lsp-ui-doc-enable t))
```

The official lsp-mode docs also recommend setting the following vars to increase its performance. Just put the following blobs in your config file:

``` emacs-lisp
;; To set the garbage collection threshold to high (100 MB) since LSP client-server communication generates a lot of output/garbage
(setq gc-cons-threshold 100000000)
;; To increase the amount of data Emacs reads from a process
(setq read-process-output-max (* 1024 1024)) 
```

That's it. You are all set. With less than 20 lines of code, you have configured your Emacs to act as an IDE for Go/Golang code. Once you are comfortable, you can continue further customization by integrating a variety of other helpful packages such as projectile (helpful when working with projects) treemacs (file browser, error tree visualization) etc. We will talk about that in another post.
