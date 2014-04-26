---
layout: post
title: "Sharing an internet connection from your computer to your mobile device"
description: "How to share an internet connection from a computer running Windows or Mac OS X or Linux to a device running iOS or Android"
date: 2013-06-19 00:13
comments: true
tags: [how-to]
---

Recently, I explored how to share an internet connection from computers running Windows or Mac OS X or Linux to mobile devices running iOS or Android.

Here's the summary:

Unrooted Android devices don't support ad-hoc mode [https://code.google.com/p/android/issues/detail?id=82](https://code.google.com/p/android/issues/detail?id=82) i.e. they don't see ad-hoc WiFi networks.

iOS devices support ad-hoc mode i.e. they can see ad-hoc WiFi networks.

Now, if you are on a Mac and you want to share your internet connection, you follow the steps outlined here: [http://support.apple.com/kb/PH3853](http://support.apple.com/kb/PH3853), this creates a WiFi network in Infrastructure mode which is visible to both iOS and Android devices and you can access the internet on these devices using this network.

You can create an ad-hoc network on a Mac by following these steps: [http://support.apple.com/kb/PH3771](http://support.apple.com/kb/PH3771). But this network is not visible to unrooted Android devices and iOS devices were not able to access the internet even after connecting to this network. So, don't do this.

If you are on a PC (Windows 7 or later), you can use [Connectify Hotspot](http://www.connectify.me/hotspot/) to create a hotspot in Access Point Mode which is visible to both Android and iOS devices and access the internet on these devices using this hotspot. However, if you are on Windows XP or Vista, your Connectify hotspot will operate in ad-hoc mode and will not work with unrooted Android devices [[1](http://support.connectify.me/entries/20307637-Does-Connectify-Hotspot-run-on-Windows-Vista-or-Windows-XP-)] [[2](http://support.connectify.me/entries/20316691-access-point-mode-vs-ad-hoc-mode)].

If you don't want to use Connectify Hotspot, you can create an ad-hoc network by following these steps: [http://windows.microsoft.com/en-in/windows7/set-up-a-computer-to-computer-ad-hoc-network](http://windows.microsoft.com/en-in/windows7/set-up-a-computer-to-computer-ad-hoc-network). This network is not visible to unrooted Android devices but is visible to iOS devices and they can access the internet using this network.

If you are on a PC running Ubuntu, you can create an ad-hoc network by going to the `Network` menu and then clicking `Create New Wireless Network`. This network is not visible to unrooted Android devices but is visible to iOS devices and they can access the internet using this network.

I don't know of a way to create a WiFi network on an Ubuntu machine which is visible to unrooted Android devices.
