---
layout: post
category: documentation
title: "Welcome 2 CS2"
author: "sneaKer"
order: 26
date: 2023-09-14
---

This guide will show you all needed steps to setup your config/buyscripts etc in CS2.

### Path for client config files ###

CS2 looks for config files in this folder (if you installed it on drive "C:")

  "C:\Program Files (x86)\Steam\steamapps\common\Counter-Strike Global Offensive\game\csgo\cfg"

Please be aware that a very similar path exists without "game" in it!!!

WRONG: "C:\Program Files (x86)\Steam\steamapps\common\Counter-Strike Global Offensive\csgo\cfg"

Correct: "C:\Program Files (x86)\Steam\steamapps\common\Counter-Strike Global Offensive\game\csgo\cfg"


### sample cfg ###

Download <a href="autoexec.cfg">this</a> file and put it into the exact folder mentioned above.
Open the file with a texteditor and change the keys for the binds as you like.
When saving the file with your changes do not change the file extension!
(if you end up with something like autoexec.cfg.txt it will not work!)

It should load automatically, if it did not try opening the console (ingame) and type exec autoexec.cfg, you then should see the loaded message from within the file.

### tbd ###

Comming soon: sample practice cfg for correct local server setup for practicing nades and
guide howto download and watch demo files from pro and friend matches aka howto stalk correctly ;)


Note that you can execute script files anytime by opening the game's console and typing

    exec <config-file-name>

