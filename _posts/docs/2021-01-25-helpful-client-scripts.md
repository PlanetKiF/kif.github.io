---
layout: post
category: documentation
title: "Helpful Client Scripts"
author: "Ciske Busch"
#date: 2021-01-25
---

The following scripts have been proven useful on the client side.

### Basic information regarding client config files ###

The Steam client looks for config files in this folder 

    <Steam>\userdata\<user-id>\730\local\cfg

where `<steam>` denotes the Steam installation folder and `<user-id>` your own Steam user id (typically there is only just on subfolder in `\userdata` so don't worry about it). `730` stands for CS:GO's App-ID. 

The file `config.cfg` is created automatically by the game and should not be edited as changes will be  overwritten. It is however a source of useful information regarding the current configuration and possible key bindings.

The file `autoxec.cfg` will be executed automatically at game start - this is were you should put your custom configuration. I myself prefer to put my configuration in a separate file which in my case is called `dackel.cfg` . By adding the line
    
    exec dackel.cfg

to the `autoexec.cfg` this will be loaded at startup as well. But this is for cosmetic reasons only.

Note that you can execute script files anytime by opening the game's console and typing

    exec <config-file-name>

Also I add the following lines at the top of the file: 

    echo ""
    echo "***********************************************"
    echo "Executing dackel.cfg v<date-last-edited>" 
    echo "***********************************************"
    echo ""
    
where `<date-last-edited>` contains you guess what.

This way I can see in the clients console that the file has been executed and also when I last edited it. 


### Fast weapon switches ###

The following scripts are used to switch between (main) gun and pistol or knife quickly.

    alias use_pistol "slot2"
    // use your main weapon - or the pistol if you do not have one
    alias use_gun "slot2;slot1"
    alias use_knife "use Weapon_knife"

    //switch between knife and gun (and reset the pistol_toggle)
    alias knife1 "use_knife; alias toggle_knife knife2; alias toggle_pistol pistol1"
    alias knife2 "use_gun; alias toggle_knife knife1; alias toggle_pistol pistol1"
    alias toggle_knife knife1
    knife2

    //switch between pistol and gun (and reset the knife_toggle)
    alias pistol1 "use_pistol; alias toggle_pistol pistol2; alias toggle_knife knife1"
    alias pistol2 "use_gun; alias toggle_pistol pistol1	; alias toggle_knife knife1"
    alias toggle_pistol pistol1
    pistol2

    bind "q" "toggle_pistol"
    bind "f" "toggle_knife"


### Bots ###

These scripts help with adding and removing bots to the server. Note that the rcon password needs to be set.

    // add a single bot
    alias abt "rcon bot_add_t expert"
    alias abc "rcon bot_add_ct expert"

    // add  8 bots for each side
    alias botsadd "abt;abc;abt;abc;abt;abc;abt;abc;abt;abc;abt;abc;abt;abc;abt;abc"

    alias botsremove "rcon bot_kick all"

    alias botsdeactivate "rcon bot_quota 0"
    alias botsactivate "rcon bot_quota 16"

    alias botshelp "echo Bots: botsadd, botsremove, botsactivate, botsdeactivate"


### Netgraph ####

This script is used to toggle the display of net_graph information

    alias netgraphon "net_graph 1; alias toggle_netgraph netgraphoff"
    alias netgraphoff "net_graph 0; alias toggle_netgraph netgraphon"
    netgraphoff

    bind "-" "toggle_netgraph"

