---
layout: post
title: "How to transfer files from Mac to iPad without iTunes"
date: 2013-10-20 09:36
comments: true
description: "How to transfer files from your Mac/PC to your iPad/iPhone/Android device without iTunes."
categories: [how-to]
---

Usually when I need to transfer files from my Mac to my iPad, I either email them to myself or use Dropbox. But sometimes I don't want to send some files over the internet. Earlier, in those cases I would connect my iPad to my Mac and then transfer the files via iTunes. Not anymore. There is a really cool way to transfer files from your Mac to your iPad when they are on the same network. Here's how:

1) Open the terminal.

2) In the termial, go to the folder which contains the files you want to transfer.

3) Enter `python -m SimpleHTTPServer 8000`.

4) If the firewall prompts: *Do you want the application "python" to accept incoming network connections?* Click *Allow*.

5) Find out the IP address of your Mac using `ifconfig`.

6) Go to `http://<your-mac's-ip-address>:8000` on your iPad.

7) Done!

This method is very general. You can use it to transfer files from your Mac or PC to your iPad, iPhone or Android device. The only requirements are that they should be on the same network, the Mac/PC should have python installed and you should have permission to poke holes in your firewall.
