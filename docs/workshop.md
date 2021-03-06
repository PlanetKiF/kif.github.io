---
layout: post
category: documentation
title: "Workshop maps"
author: "Michael Wolber"
order: 20
date: 2021-02-03
---

We like to play a lot of old school maps and were really happy to find adoptions of them for CS:GO. Rats! 747! Mansion! But how do we get them into today's game infrastructure?

The easy way to do so is to directly add the *.bsp files to the servers `maps` directory and to distribute a map-pack archive to all participants.

There are a number of disadvantages of this approach:

 * everybody needs to copy the files from the map-pack to one's local `maps` folder. And there is always _one_ who did not do that
 * for a just-in-time installation you need an additional Fast Download server
 * updates, that means adding new maps, get really complicated

We used that way quite for a while, as the better solution (see below) was not working on our former hoster.

## Solution

Since a few years there is a possibility to use the steam infrastructure to select and distribute your preferred maps automatically. The Steam Workshop.

The server uses a _map collection_ on the Steam workshop to control which maps are used on the server. We defined the [kifserver](https://steamcommunity.com/sharedfiles/filedetails/?id=2187570436) collection.

### How to make the server use the workshop
Three steps:
1. Steam Web Api Key
2. Map collection to use
3. Startup Map specification


#### 1. Get a  Steam Web API Key

To access the map collection on the workshop the server needs to use the Steam Web API. Therefore it needs an access key to that API.

Register a new API Key at [https://steamcommunity.com/dev/apikey](https://steamcommunity.com/dev/apikey)

Copy your new  key (something like `25B72DCA80CAEC816D65398C321E188A`) and put it into blank file called `webapi_authkey.txt` in your servers CSGO folder , e.g. `csgo_ds/csgo`.


#### 2. Tell the server which collection to follow
Simply put the command

```
host_workshop_collection 123456789
```

to your autoexec.cfg, replacing 123456789 with your collections ID. You can find your ID quite easily: open it with your browser in the workshop and take a look at the URL: it will be something like
```
https://steamcommunity.com/sharedfiles/filedetails/?id=123456789
```
That last parameter is your id.

To make the server inform you about its progress when it is downloading a new map you can put the additional line
```
sv_broadcast_ugc_downloads 1
```
into your autoexec.cfg. As this is rarely the case this is not really needed, though it sounds nice.

#### 3. Specify the startup map

When using the Workshop Mode you cannot specify the startup map of the server by using the `+map` start parameter. This seems to be more or less undocumented, and though the trick is very simple, it took us quite a while to sort this out.

The start-map is simply defined by the sequence of maps in your Steam workshop map collection! When in editing mode, you can simply drag&drop the maps to the order you like. The first one is the map the server uses as the startup map.

Or in other words: for as long as there is no manual `changelevel` or mapvote, the server will simply follow the list of maps in your collection.

### A thing to take care of

 When switching the server to _Workshop Mode_ it will no longer use the standard maps that come preinstalled. The get around that the CS team provided an [_official_ map collection](https://steamcommunity.com/sharedfiles/filedetails/?id=264837167), that you can add to your servers map collection. You can find that collection in the workshop as well.


