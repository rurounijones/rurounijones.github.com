---
layout: post
title: "Setting up Japanese input on Kubuntu 12.04"
date: 2012-12-28 02:41
comments: false
categories: [kubuntu]
keywords: kubuntu japanese ibus
---

Historically setting up Japanese input on Linux has been a trial-and-error affair with much forum searching, dark config file incantations and other black magic.

Luckily with Kubuntu 12.04 getting setup with Japanese input has become a very simple process

<!--more-->

I am going to make a few assumptions before we get going. I am going to assume that:

9. You have installed Kubuntu 12.04 or later
9. You installed the English version.
9. You have not yet tried to setup Japanese text input yet.

The reason for 1. is simply that I have not tested this on any earlier versions. Feel free to give it a go and report back, but no guarantees.

The reason for 2. is again, because I have not tested it with other languages. Feel free to give it a go and report back, but no guarantees.

The reason for 3. is because, historically, attempting ot get Japanese input working with one method would intefere with other attempts using different methods.
If you have already tried to setup Japanese input on your install then by all means try the below method, but if it doesn't work I am afraid
that I cannot help you. In this case the best (albeit annoying) option is to reinstall.

Right, with these caveats out of the way lets proceed.

```
sudo apt-get install ibus-mozc gnome-icon-theme
```