---
layout: post
category: documentation
title: "Managing the Server"
date: 2021-01-24
---

How to manage access to your server and how to start, stop and reboot CS:GO.

Note: 

* this text refers to an installation as described in "[Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %})". If your server uses different pathnames make sure to adjust the information below accordingly.

 * when we talk about the "server" we mean the linux server which is running the CS:GO game server. 


## Accessing the server ##

To manage the game server ssh and ftp access to the (linux) server is all a game server's admin needs. So server admins do not require any access to the AWS account.

### Access by ssh ###

If you are on Linux then install OpenSSH (just google it). If you are Windows then use the in build OpenSSH feature. "[Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %})" gives you information on how to do that.

To access your server you will need the key file that you got during the server installation. Alternativly you can create new files (e.g. one for each server administrator).

Open your Command Prompt and type 

    ssh -i <path-to-my-keypair.pem> ubuntu@<instance-ip>.

A more comfortable way to connect to your server is to create a ssh config file like this

    Host *
        RemoteForward 52698 localhost:52698
        AddKeysToAgent yes
        
    Host <myalias>
        Hostname <hostname-or-ip>
        User ubuntu
        IdentityFile <path-to-key-file>

On Windows the config file is called 'config' (without extension) and by default needs to be saved to this location:  `C:\Users\<user>\.ssh\`

Now you can access your server just by typing

    ssh <myalias>

### FTP Access using  Filezilla ###

For manually transfering files the ftp client Filezilla is a good choice. When configuring your server use the following values

* Protocol: SFTP
* Host: <your-server-ip>
* Port: 
* Logon Type: Key File
* User: Ubuntu
* Key-File: <path-to-key-file>


## Linux server updates ##

tbd


## Starting & stopping the game serer ##

In our configuration the CS:GO game server starts automatically whenever the (Linux) server (re-) starts. So if you want to restart the game just log into the server using ssh and reboot the server by typing 

    sudo shutdown -r now

If the game server is started automatically you do not have direct access via server console when  logging on to the server using ssh - so how to stop it?

Stopping the game server should be possible by using `steamcmd` - however we never really got this to work. So we just kill all the processes related to the game: 

    killall -9 srcds_linux srcds_run 

If you do not want the game server to be automatically started you have to remove / comment out the line we added in the crontab (see "[Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %})").

Starting the game server manually can be achieved by typing

    ~/csgosv/srcds_run -game csgo <your-command-line-options>
 
To stop just hit `CTRL-C`,.


## Updating the game server ##

To update the game server installation log into the server using ssh and type

    steamcmd +login anonymous +force_install_dir ~/csgosv/ +app_update 740 +quit

Note: if you run into a problem check your pathnames and if you have enough free disk space on your server. See [Increasing disk space]({% post_url docs/2021-01-27-setting-up-the-server %}) for more information.

## CS:GO Logfiles ##

tbd

## Output of the CS:GO server console ##

If you (auto-) start your server using the crontab you won't see any output when logging into the server using ssh. To watch the output you need to activate the logging of the console output and then 'tail' the output to your current window.

To log the server console output into a logfile add the following command-line parameters to the game's launch options 

* `-condebug` activates the output of the server's console into the file ~/csgosv/csgo/console.log 
* `-conclearlog` clears the log file

Connect to your server using ssh and type

    tail -f ~/csgosv/csgo/console.log

You will now see the output of the server's console.

** Be aware ** Only use this with caution and while you are monitorin the file as the log file can grow very quickly and might even fill up your whole disk space.




