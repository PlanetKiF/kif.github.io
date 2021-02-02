---
layout: post
category: documentation
title: "Managing the Server"
date: 2021-01-24
---

How to manage access to your server and how to start, stop and reboot CS:GO.


* Naming (Linux) Server vs. Game Server
* Access via AWS vs. direct access
* Access by ssh
* Access by filezilla
* linux server updates
* rebooting the linux server



## Updating CS:GO ##

To update the game server installation log into the server using ssh and type

    steamcmd +login anonymous +force_install_dir ~/csgosv/ +app_update 740 +quit

Note: if you run into a problem check your pathnames and if you have enough free disk space on your server. See [Increasing disk space]({% post_url docs/2021-01-15-setting-up-the-server %}) for more information.

## Using steamcmd to start / stop / restart the game serer ##

...

## CS:GO Logfiles ##

...

## Output of the CS:GO server console ##

If you (auto-) start your server using the crontab you won't see any output when logging into the server using ssh. To watch the output you need to activate the logging of the console output and then 'tail' the output to your current window.

To log the server console output into a logfile add the following command-line parameters to the game's launch options 

* `-condebug` activates the output of the server's console into the file ~/csgosv/csgo/console.log 
* `-conclearlog` clears the log file

Connect to your server using ssh and type

    tail -f ~/csgosv/csgo/console.log

You will now see the output of the server's console.

** Be aware ** Only use this with caution and while you are monitorin the file as the log file can grow very quickly and might even fill up your whole disk space.




