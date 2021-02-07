---
layout: post
category: documentation
title: "Managing the Server"
date: 2021-01-24
---

How to manage access to your server and how to start, stop and reboot CS:GO.

Note: 

* This text refers to an installation as described in [Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %}). If your server uses different path names make sure to adjust the information below accordingly.

 * When we talk about the "server" we mean the Linux server which is running the CS:GO "game server". 


## Accessing the server ##

To manage the game server people only need ssh and ftp access to the (Linux) server. They do not require any access to the AWS account (which we mention because we wasted quite some time fiddling around with AWS rights management).

### Access by ssh ###

If you are on Linux then install OpenSSH (just google it). If you are Windows then use the in build OpenSSH feature. [Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %}) gives you information on how to do that.

To access your server you will need the key file that you got during the server installation. Alternatively you can create new files (e.g. one for each server admin).

On windows open your command prompt and type 

    ssh -i <path-to-my-keypair.pem> ubuntu@<instance-ip>.

This will open a ssh connection on the Linux server.

A more comfortable way to connect to your server is to create a ssh config file like this

    Host *
        RemoteForward 52698 localhost:52698
        AddKeysToAgent yes
        
    Host <myalias>
        Hostname <hostname-or-ip>
        User ubuntu
        IdentityFile <path-to-key-file>

On Windows by default the config file is called 'config' (without extension) and needs to be saved to this location:  `C:\Users\<user>\.ssh\`

Now you can access your server just by typing

    ssh <myalias>

### FTP Access using  Filezilla ###

To manage server files the ftp client Filezilla is a good choice. When configuring access to your server use the following values

* Protocol: SFTP
* Host: `<your-server-ip>`
* Port: 
* Logon Type: Key File
* User: Ubuntu
* Key-File: `<path-to-key-file>`


## Linux server updates ##

This depends highly on the Linux distribution of your choice. If you like a Debian based distro as we did with Ubuntu, then an update of the current package information followed by the update command will do.

```
sudo apt-get update && sudo apt-get -y upgrade
```

If you like housekeeping, then you could do a `sudo apt autoremove` from time to time. And if you like to live on the dangerous side of life do a `sudo apt full-upgrade` without checking the messages.

Of course it is recommended to read the messages arising during the update opgerations. A major distribution upgrade might be waiting. You can install this by running `sudo apt-get  dist-upgrade`, but you should think twice before doing this without a backup.
## Starting, stopping and rebooting the game server ##

In our configuration the CS:GO game server starts automatically whenever the (Linux) server (re-) starts. So if you want to restart the game server you can just log into the server using ssh and reboot the server by typing 

    sudo shutdown -r now

If the game server is started automatically you do not have direct access via server console - so how to stop it?

Stopping the game server should be possible by using `steamcmd` - however we never really got this to work. So instead we just use the Linux kill command to kill the process of the game server.

To kill a process you need to know the Process ID (PID). You can find that out using the Linux command "top" and look for the PID of the process "srcds_linux" (which should be at the very top). Then type

    kill -9 <pid>

However (if not configured differently) the game server will restart immediately  automatically (with the original launch options). So this basically leads to a restart of the game server.

If you do not want the game server to restart automatically you also need to kill the control process which manages the game server's restart. For this use 
 
     killall -9 srcds_Linux srcds_run 

Note: If you do not want the game server to be automatically started when rebooting the server you have to remove / comment out the line we added in the crontab (see [Setting up the Server]({% post_url 2021-01-27-setting-up-the-server %})).

Starting the game server manually can be achieved by typing

    ~/csgosv/srcds_run -game csgo <your-command-line-options>
 
To stop just hit `CTRL-C`.

Command line options are explained [here]({% post_url 2021-01-20-configuring-the-game-server %}).


## Updating the game server ##

To update the game server installation log into the server using ssh and type

    steamcmd +login anonymous +force_install_dir ~/csgosv/ +app_update 740 +quit

Note: if you run into a problem check your path names and if you have enough free disk space on your server. See [Increasing disk space]({% post_url docs/2021-01-27-setting-up-the-server %}) for more information.

## CS:GO Logfiles ##

tbd

## Output of the CS:GO server console ##

If you (auto-) start your server using the crontab you won't see any output when logging into the server using ssh. To watch the output you need to activate the logging of the console output and then 'tail' the output to your current window.

To log the server console output into a log file add the following command-line parameters to the game's launch options (see [here]({% post_url 2021-01-20-configuring-the-game-server %}) for more info on that).

* `-condebug` activates the output of the server's console into the file ~/csgosv/csgo/console.log 
* `-conclearlog` clears the log file

Connect to your server using ssh and type

    tail -f ~/csgosv/csgo/console.log

You will now see the output of the server's console.

**Be aware**: Only use this with caution and while you are monitoring the file as the log file can grow very large very quickly and might even fill up your whole disk space.




