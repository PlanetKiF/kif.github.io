---
layout: post
category: documentation
title: "Helpful Client Scripts"
#date: 2021-01-25
---

The following scripts have been proven useful on the client side


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

