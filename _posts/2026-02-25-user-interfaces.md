---
layout: post
title:  "LUNA's UI Themes"
date:   2026-02-25 18:58:00 -0300
categories: eyasoft luna-clients
---
Today we are going to talk some about LUNA Online's user interface themes, a bit of a detour from our last few posts on reverse engineering of the various foolish things private server owners like to keep doing, to be sure, but hopefully an interesting topic nonetheless. This was brought on by a recent discussion with my good friend Porky where custom UI's for LUNA came up.

## Official Themes

Most people who have played LUNA at some point will be familiar with its traditional theme, which is largely the same across Classic and Plus, other than minor alterations for various new/changed features. It is available in three color variations on essentially every official LUNA publisher and private server, and they're all identical other than their coloring. The default color is blue, however gray and pink are also available, selectable via a dropdown menu in the launcher.

![Screenshot showing the three color variations of the standard theme](/images/LUNA_OfficialThemes.png)

At the time LUNA was introduced, having changeable UI themes was not very common amongst similar games, so while not necessarily a selling point, it did bring enjoyment for a lot of users. It also meant that having the default theme / color being a fairly vivid blue was a much safer decision then it would have been if it couldn't be changed out.

You may be wondering, why only three colors? Why didn't they ever add more options? That is largely due to technical limitations. Due to how LUNA's interface works, changing the theme involves changing out the entire set of images used for the background of all the game windows. So adding a new color option would have required remaking dozens of files. While this should have been trivial for EyaSoft to do, starting with the gray theme and adjusting the tint from there, they never did, which does suggest they may have lost at least some of the unflattened source files the UI was made from, making adjusting the base tint much more challenging without affecting other parts of the UI that shouldn't be colored. Given what we know about EyaSoft, this does seem like a very real possibility.

The actual act of changing the theme in the launcher causes it to write out a different path into a config file, which the client then uses to decide which `.BIN` file to load the image paths from. This enables selecting a different `.BIN` containing a different set of image paths, in turn allowing the UI images themselves to be swapped out. While loading the default theme, image ID `2` might point to `2.tif`, while loading the pink theme would result in image ID `2` pointing to `2_pink.tif` for example.

### The green one

Unknown to most is that there was at one point a fourth option, a green colored UI theme, with different styling than the other colors. This theme actually predates the other three and was used only during early playtests, before the game even entered closed beta testing more publicly. This theme was seemingly discarded after these early playtests in favor of the color-changeable one we all came to know and love. Whether it was always intended to be discarded and was just a placeholder or was redone due to feedback during those playtests, is unknown to me. It has a much simpler, flatter design than the final ones did, which may lean towards it being intended as a temporary placeholder while the actual UI layout was still in flex.

![Screenshot showing the forgotten fourth color, green, from early test builds of LUNA](/images/LUNA_GreenTheme.png)

Sadly, there is very little imagery of this theme available, mostly sourced from now defunct news articles, and as such the image above is pieced together from several different screenshots. I have been unable to source a client containing any of the actual assets for this theme, despite cataloging several dozen LUNA installers, including the earliest ones seemingly publicly available. Extensive digging in the archives of EyaSoft's korean site revealed the URLs and filenames of earlier clients that likely contained this UI, however sadly the clients themselves weren't archived and don't seem to have been mirrored anywhere. If by some miracle you have one of the following files, or even an earlier one, rotting on a hard drive, please get in contact with me via a comment below or via email.

```
LunaClient_07110101.exe
LunaClient_07120701.exe
LunaClient_07121801.exe
LunaTestClient_07121801.exe
LunaClient_07120701.exe
LunaTestClient_08022701.exe
LunaClient_08052201.exe
LunaClient_08052201.exe
LunaTestClient_09070901.exe
LunaClient_09012201.exe
```

## Unofficial Themes
Despite what was implied about retheming LUNA being fairly difficult just due to the volume of imagery that would need to be recolored or recreated entirely, some private servers over the years have actually done so, with varying degrees of success.

This list will be updated as new clients are discovered that contain alternative themes, so check back occasionally if this is something you're interested in.

### Rise LUNA's Crystal Themes - 2013
Rise LUNA was the first server, to my knowledge, to attempt a custom theme, and the only one to do so with more than one color variation. In fact, they even tripled the normal three color options, giving six different coloring options for their new theme. The files themselves refer to these as `Crystal` colors, and that description is fairly accurate, they do resemble crystal, or perhaps colored glass, as they feature higher transparency than the default UI does, which can lead to the windows being hard to read on some backgrounds unfortunately.

RISE was also one of the first servers to feature PAK file obfuscation, which is likely why these themes haven't been seen in use anywhere else, no one figured out how to steal them at the time, and by the time anyone else cared enough about LUNA to be looking into custom themes, Rise was long forgotten.

![Screenshot showing the six color variation's of RISE LUNA's crystal theme](/images/LUNA_CrystalThemes.png)

### Eclipse LUNA's Gray and Gold Themes - 2020
Eclipse was one of the first in a second wave of custom themed private servers. The exact timeline of who was first, or who originally created what themes, gets murky from here out, because the rest are used across several servers, whether that's from the same people rebooting their servers under new names or from people ripping off their files is unknown.

First up is a theme eclipse simply referred to as gray, and it is indeed a variation on the default EyaSoft gray theme. It doesn't appear to have been finished, as only about 80% of the windows have been re-skinned, which is why the below screenshot doesn't feature the quest window like the rest of the comparison shots, as the quest window is just the normal gray skin, rather than being custom.

![Screenshot showing Eclipse LUNA's slightly unfinished gray theme](/images/LUNA_EclipseGrayTheme.png)

In addition to this gray theme, Eclipse also contains a gold and brown colored theme that is heavily inspired by, and uses assets from, Legend of EDDA. This theme is essentially a direct port of EDDA's styling to LUNA, for better or worse. It doesn't necessarily look quite right, as the window frames are quite different between the two games. In addition, the inventory window icon positions don't exactly match the standard layout, causing some graphical anomalies in this demo image. While these icon positions are adjustable, doing so would make you not work with any of the themes using the standard locations, limiting you to only this theme.

![Screenshot showing Eclipse LUNA's slightly odd gold theme](/images/LUNA_EclipseGoldTheme.png)

### Glorious, Helios, Great, iPlay, Unicorn LUNA's Gold Theme - 2020-23
This theme is shared across five different servers that I know of and is largely brown and gold in appearance, just like Eclipse's Gold Theme. Further examination reveals that this seems to be an evolution of that theme, many of the windows, such as the Quest window, are identical across both Gold Themes, whereas the ones that previously featured badly done ports of EDDA's window borders have been redone with something more suited to LUNA's window design, while still maintaining the overall design and color scheme.

The fact that this theme is present in so many servers across several years does lend credit to the theory that all of these servers are run by the same people and are simply being recycled with a new name and logo every so often as the player base thins out.

This is arguably the "best" theme in my opinion, at least from a design standpoint, being quite visually appealing without just being a direct ripoff of Legend of EDDA.

![Screenshot showing the second version of the gold theme](/images/LUNA_GloriousGoldTheme.png)

#### Addendum: Legend of EDDA

Although not LUNA, another EyaSoft game has an interesting theme tidbit that's worth mentioning, Legend of EDDA, or just EDDA for short, contains unused remnants of a prototype UI, similar in purpose to LUNA's green placeholder UI. EDDA has a fairly intricate, gold and brown theme, with lots of detailing, compared to the flatter UI LUNA uses, however there are unreferenced images for a darker, gray and black theme that is much less detailed, quite close to the design philosophy of Mosiang or Titan Online, in truth. There is no screenshots or information on this UI, it was never shown publicly as far as I can tell, and EDDA's original first playtest used the same finalized theme as later versions. Early placeholder art that was mistakenly left in? Or perhaps it just represents a shift in the design philosophy.

In the example below, the normal EDDA theme is in gold on the left, whereas the prototype is dark gray and on the right.

![Screenshot showing a comparison of EDDA's normal and prototype themes](/images/EDDA_Themes.png)