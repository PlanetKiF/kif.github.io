---
layout: post
category: documentation
title: "Configuring the Game Server"
author: "Ciske Busch"
#date: 2021-01-15
---

There are more then 3000 variables and commands to configure the client and server behavior  of CS:GO. Here we try to give you a basic understanding of how configuration works, how to find information about configuration and how we configured our server.

## Sources of information ##

There is a lot of information to be found on the internet regarding CS:GO configuration. Of course [Valve](https://www.valvesoftware.com/) itself has the "definitive" information for [Advanced Server Configuration](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers#Advanced_Configuration) and [Command Line Options](https://developer.valvesoftware.com/wiki/Command_Line_Options).

We also found [TotalCSGO](https://totalcsgo.com/)'s information for [Commands](https://totalcsgo.com/commands) and [Launch Options](https://totalcsgo.com/launch-options) very helpful.

And for nearly all questions you have, Google will be your friend. However, as CS has been around for a long time there is quite a bit of outdated information to be found. So one very useful bit of
information we stumbled upon was that CS:GO itself has an in build documentation feature. If you
start your CS:GO client, open the developer console and type

    cvarlist log output.txt

you will get a text file with all the vars and commands currently working in your version of CS:GO. You'll find the file in your local CS:GO game directory.

## Basic Information ##

GS:GO can be configured using
- launch commands or options which are executed when starting the game.
- the `autoexec.cfg` file which is executed when the server is started
- the `server.cfg` file which is executed when a new map is started
- the `gamemode_xxx.cfg` and `gamemode_xxx_server.cfg` files which are executed when a new map is started (and after the `server.cfg`)

There are of course lots of other files managing the game's behavior but so far we have only changed the bot configuration file `botprofile.db`.

All the config files need to be placed in the folder `~/csgosv/csgo/cfg/`


## Server launch options ##

GS:GO command line options are used whenever you start the game. Pure command line options start with the character `-`, like `-tickrate 128`. You can also add every other server variable using the character `+`, e.g. `+game_type 0` and `+game_mode 0`. However in our opinion these are much better placed in the `autoexec.cfg` config file as this is executed at startup as well.


We currently only use the following options

    -usercon        // enable rcon access
    -tickrate 128   // set the server tickrate to 128 (as oposed to 64)

And as a result our start command looks like this:

    ~/csgosv/srcds_run -game csgo -usercon -tickrate 128

As described [here]({% post_url docs/2021-01-10-managing-the-server %}) in some cases you might want to add

     -condebug      // write the output of the server's console to the file ~/csgosv/csgo/console.log
     -conclearlog   // clear the log file at startup.

But as mentioned there beware of the potential log file size.


## autoexec.cfg ##

The `autoexec.cfg` is executed once at the startup of the server. It should therefore set the values for variables that are required to run the server like the server's hostname, passwords, Steam account ID and so on.

Ours looks like this

    hostname <host name>                     // Hostname for server.
    sv_password <password>                   // Server password for entry into multiplayer games
    rcon_password <password>                 // remote console password.
    sv_tags <e.g. your clan tag>             // Server tags. Used to provide extra information to clients when they´re browsing for servers. Separate tags with a comma.
    sv_region "3"                            // The region of the world to report this server in. (Default: -1)
    sv_setsteamaccount <steamaccountid>      // game server account token to use for logging in to a persistent game server account

    game_mode 0                                       // The current game mode (based on game type). See GameModes.txt. (Default: 0)
    game_type 0                                       // The current game type. See GameModes.txt. (Default: 0)

    sv_cheats 0                                       // Allow cheats on server (Default: 0)
    sv_lan 0                                          // Server is a lan server ( no heartbeat, no authentication, no non-class C addresses ) (Default: 0)
    sv_pure 1                                         // if set > 0 the server will ensure that the content (materials, models, and sounds) on client machines matches what is specified.
    sv_pure_kick_clients 0                            // If set to 1, the server will kick clients with mismatching files. Otherwise, it will issue a warning to the client. (Default: 1)

    sv_allow_votes 1                                  // Allow voting? (Default: 1)
    sv_damage_print_enable 1                          // Turn this off to disable the player´s damage feed in the console after getting killed. (Default: 1)
    sv_auto_full_alltalk_during_warmup_half_end 1     // When enabled will automatically turn on full all talk mode in warmup, at halftime and at the end of the match (Default: 1)


    exec banned_user.cfg                              // Load script file containing banned users.
    exec banned_ip.cfg                                // Load script file containing banned user's ips.

    game_mode 0                                       // The current game mode (based on game type). See GameModes.txt. (Default: 0)
    game_type 0                                       // The current game type. See GameModes.txt. (Default: 0)

    host_workshop_collection <collection-id>          // Get the latest version of maps in a workshop collection and host them as a maplist.
    workshop_start_map <id-of-first-map-to-play>      // id must fit to a map which is part of the above collection

You will notice that we use a workshop (map) collection for maps. [Here]({% post_url docs/2021-01-20-workshop %}) we explain in detail how this works. If you do not want to use workshops you can instead use the `map` and the `mapgroup` commands.

### server.cfg ###

The `server.cfg` is executed whenever a map change occurs. As many values change with regard to the current game mode our version of the config does not contain many values:

    mp_endmatch_votenextmap 1                         // Whether or not players vote for the next map at the end of the match when the final scoreboard comes up (Default: 1)

    writeid                                           // Writes a list of permanently-banned user IDs to banned_user.cfg. (Default: cmd)
    writeip                                           // Save the ban list to banned_ip.cfg. (Default: cmd)


### Game Mode Configs ###

In CS:GO each game mode (casual, competitive, deathmatch, ...) has it's own config files. We describe how this works by using the game mode "casual" as example.

* The config file `gamemode_casual.cfg` is part of the installation and can be overwritten by every update of the game files. So do not change anything in this file.
* Changes are made in the file  `gamemode_casual_server.cfg`. As this configuration file is executed after every other configuration file (when game mode casual is active) the settings in this file overwrite all settings in all other files.


 Our gamemode_casual_server.cfg contains the following settings (they should be pretty self explanatory).

    mp_warmuptime 30                                  // How long the warmup period lasts. Changing this value resets warmup. (Default: 90)
    mp_forcecamera 1                                  // Restricts spectator modes for dead players. 0 = Any team. 1 = Only own team. 2 = No one; fade to black on death (previously mp_ (Default: 0)
    mp_solid_teammates 0                              // How solid are teammates: 0 = transparent; 1 = fully solid; 2 = can stand on top of heads (Default: 2)
    sv_deadtalk 1                                     // Dead players can speak (voice, text) to the living (Default: 0)
    sv_talk_enemy_dead 0                              // Dead players can hear all dead enemy communication (voice, chat) (Default: 1)
    sv_talk_enemy_living 0                            // Living players can hear all living enemy communication (voice, chat) (Default: 0)
    mp_round_restart_delay 5                          // Number of seconds to delay before restarting a round after a win (Default: 10)
    mp_maxmoney 16000                                 // maximum amount of money allowed in a player's account

    mp_endmatch_votenextmap	1                         // Whether or not players vote for the next map at the end of the match when the final scoreboard comes up
    mp_endmatch_votenextleveltime	20                  // If mp_endmatch_votenextmap is set, players have this much time to vote on the next map at match end.
    mp_endmatch_votenextmap_keepcurrent	0             // If set, keeps the current map in the list of voting options.  If not set, the current map will not appear in the list of voting
    sv_vote_to_changelevel_before_match_point 0       // Restricts vote to change level to rounds prior to match point (default 0, vote is never disallowed)
    sv_vote_issue_kick_allowed 1                      // Can people hold votes to kick players from the server? (Default: 1)
    mp_autokick 0                                     // Kick idle/team-killing/team-damaging players (Default: 1)


    bot_quota 16                                      // Determines the total number of bots in the game. (Default: 10)
    bot_quota_mode "fill"                             //  The mode that the number of bots should be controlled by. Options are: normal, fill and match. Normal is normal behaviour. Fill will fill the server with as many bots as "bot_quota" is set to. Match will keep a 1 human : bot_quota ratio of bots. - Original Helptext: Determines the type of quota. Allowed values: 'normal', 'fill', and 'match'. If 'fill', the server will adjust bots to keep N p (Default: 0)
    bot_difficulty 3                                  // Defines the skill of bots joining the game.  Values are: 0=easy, 1=normal, 2=hard, 3=expert. (Default: 1)

**Note**: To turn on friendly fire you not only have to activate the variable `mp_friendlyfire' but also have to overwrite some settings controlling how much damage a players action does to his own team.


    mp_friendlyfire 1                                 // Allows team members to injure other members of their team (Default: 1)
    ff_damage_bullet_penetration 0                    // If friendly fire is off, this will scale the penetration power and damage a bullet does when penetrating another friendly playe (Default: 0)
    ff_damage_decoy_explosion 0                       // Enables or disables team damage from decoy detonation (Default: 0)
    ff_damage_grenade_hit 1                           // Enables or disables team damage from thrown grenade impacts (Default: 1)
    ff_damage_reduction_bullets 0.33                  // How much to reduce damage done to teammates when shot.  Range is from 0 - 1 (with 1 being damage equal to what is done to an en (Default: 0)
    ff_damage_reduction_grenade 0.85                  // How much to reduce damage done to teammates by a thrown grenade.  Range is from 0 - 1 (with 1 being damage equal to what is don (Default: 0)
    ff_damage_reduction_grenade_self 1                // How much to damage a player does to himself with his own grenade.  Range is from 0 - 1 (with 1 being damage equal to what is do (Default: 0)
    ff_damage_reduction_other 0.4                     // How much to reduce damage done to teammates by things other than bullets and grenades.  Range is from 0 - 1 (with 1 being damag (Default: 0)
    mp_tkpunish 1                                     // Will TK´ers and team damagers be punished in the next round?  {0=no,  1=yes} (Default: 1)

The following turns on the "Kill Cam" which plays a short video of the kill scene from the killer's perspective:

    tv_enable 1                                       // Required for kill cam. - Original Helptext: Activates GOTV on server (0=off;1=on;2=on when reserved) (Default: 0)
    spec_replay_bot 1                                 // Enable Spectator Hltv Replay when killed by bot (Default: 0)
    spec_replay_enable 1                              // Enable Killer Replay, requires hltv server running (0:off, 1:default, 2:force) (Default: 1)

## Bot Configuration ##

tbd




