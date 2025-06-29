---
layout: post
title:  "The File Types Of SS3D"
date:   2025-06-21 13:20:00 -0300
categories: eyasoft ss3d
---
This post serves as an overview of all of the custom filetypes SS3D makes use of and their general purpose. The list is in alphabetical order, except where closely related file types have been grouped together. In a future series of posts, we will dive deeper into some of these.

## \*.ANM
ANM files store an animation, typically for a specific MOD file, and the only way to generate them is using SS3D's vintage 3DSMax8 exporters.

## \*.BIN
BIN files are one of the most widespread file types, used to store just about everything, from item names to quest scripts, dialog strings to server IPs, and much more. In the earliest versions of Mosiang, these were just .txt files instead, and at their core they continue to be just text files, encoded to prevent modification. Being text files means the data within can, and does, take just about any shape you can imagine, from simple lists, to complex XML like quest scripting syntax. Their actual format on-disk varies through the years, starting with a simple binary encoding in the first games, and eventually adding an AES encryption layer on top of that. Depending on what region they were intended for and when, their contents may be ASCII, or they may be multi-byte wide chars, more commonly known as UTF16, to allow them to store various non-English characters.

## \*.BEFF, \*.BEFL, \*.BMHM, \*.BSAD
These four file types are nothing but normal BIN files with a different extension. They originated in very early versions of Mosiang, where these files lacked the B prefix (example: \*.MHM), and were just plain text files with special extensions to indicate their special purposes amongst all the other text files. When the BIN file was introduced to prevent files from being tampered with by users, EyaSoft just added a B to the front of each of these existing special extensions.

BEFF and BEFL are related, BEFF files contain individual effect scripts, while BEFL files are lists of those BEFF effect files. BEFF being "Binary EFFect", and BEFL being "Binary Effect File List". The BEFF files describe each effect, what textures, sounds, and behavior it has, while the BEFL effect lists allow the client to select the correct list of effect files to use depending on character race and gender.

BMHM files are map information files, that give the client information about each individual map, such as what skybox to display, what ambient lighting to use, etc. The exact reason behind the BMHM name isn't as obvious as the rest, but appears to stand for "Binary MH Map", with MH being the abbreviation EyaSoft internally referred to Mosiang with.

BSAD files are a bit more difficult to describe, as they aren't actually in use any more, in any of the source code I have access to. It is possible they were never actually used by the clients and are just a remnant of some early unfinished feature. BSAD stands for "Binary Skill Area Definition". They seem to represent skill behavior and range, but the limited actual examples of these files make determining how exactly they did that likely impossible.

## \*.CHR, \*.CHX
CHR and CHX are both plain text files and both serve a nearly identical purpose, bundling together a MOD model file and a variable quantity of ANM animations and/or MTL material files. The only difference between the two file types is that CHX supports multiple model files, each with their own lists of animations and materials. This enables, for example, loading a pair of dual daggers in one go, or loading all the parts of the player model (body, hair, face, hands, and feet) together.

Interestingly, despite only CHX supporting multiple models, many of the CHR files include a `*MOD_FILE_NUM	1` directive, despite this not being something the CHR loader looks for or makes use of and is only intended to be inside a CHX file. This is most likely the result of copy & paste errors by EyaSoft developers accumulating over time.

## \*.HFL
HFL stores what EyaSoft calls a HeightField. This is essentially the terrain of the map itself that the players, NPCs, monsters, etc all walk on top of. It's a fairly complex format, as instead of just storing the map as a giant grid of heights & what textures to apply where, it contains extra information needed for performing LOD modifications to the terrain in real time.

This means that, by default, terrain directly under the character is more detailed than terrain farther away. Although the HeightField system supports up to 9 LOD levels, all existing maps only make use of 3, each losing roughly half of the detail of the previous level. Although this does result in far-away terrain looking less than perfect, it was a very necessary trade off for performance at the time SS3D was created. Interestingly, Legend of EDDA has this system disabled, and the entire HeightField forced to maximum detail at all times.

## \*.LM
LM is an interesting case, it is a binary file that is supposed to store the lightmap for each maps terrain. Despite the engine fully supporting loading these lightmaps, as well as the map editor seemingly being capable of generating them, there isn't a single one in use anywhere across the entire SS3D portfolio. Every MAP file does specify a lightmap file to load, but every one is a completely empty file. Why did they add this capability and then never use it? No idea. 

## \*.MAP
These files, as their file extension suggests, store most of the actual information that makes up each individual map. They're a plain text file, containing 5 different sections containing all the different information that makes up a map at the engine level.

These include the GX_METADATA section containing the maps bounding box size, and in some later map files, some miscellaneous information used by newer versions of the map editor. GX_MAP containing the names of the Static Model and HeightField files for the map, GX_OBJECT containing each and every individual model on the map, as well as its position, rotation, and scale. GX_LIGHT containing the maps lights, their positions, colors, and intensities. These are only used by the map editor to generate the VCH and VCL vertex coloring files for the map, as SS3D doesn't support real time lighting. There is also a fifth section, GX_TRIGGER, that is unused and empty, but required to still be present as the engine expects it to be there. This seems to have been for a feature that was abandoned early on.

## \*.MTL
MTL is a material file, it contains metadata about a material used by a MOD file, and can be loaded by SS3D to alter a material already in use by the model. The main usage of these files is to change the texture applied to the face of the player characters for various emotes and effects, by altering the texture specified for the face's material. They're generated by a roughly 2 million year old 3DSMax plugin.

## \*.MOD
MOD is SS3D's custom model format, and like ANM and MTL it can only be generated with a very old 3DSMax plugin. They contain vertex data, as well as materials specifying the models appearance and textures, and bones used in conjunction with ANM files for animations. Unfortunately, SS3D's relative uncommonness means that the vast majority of model format converters can't convert MOD at all, and the couple that do, don't handle more complex models correctly.

## \*.OPT
This file type is not technically an SS3D one, but is generated by each game to store the users settings, such as BGM volume or the draw distance. They are a binary file, and their format varies between games, and even versions of the same game, as options are added and removed over time.

## [\*.PAK]({% post_url 2025-06-29-pak-files %})
PAK is probably the most complex file format that SS3D has, as well as being one of the most important. As you may have guessed, PAK stands for Pack, and the purpose of this filetype is to contain other files. An easy way to conceptualize them is as a ZIP file, but without any compression.

Their actual on-disk format consists of a fairly large main header, followed by a per-file header and the actual contents of the file. Despite this sounding fairly simple, the actual implementation is quite complex, and full of pitfalls and potential issues, which I will go over in a dedicated post later on. Despite all of the issues with the format, EyaSoft has continued to use PAK files in each client, to prevent players from being able to access or modify files. I can only surmise they decided all the trade-offs were worth the protection at the time.

## \*.STM
STM, which stands for Static Model, its purpose is to store large portions of the models on each map. In a typical map, it might contain all the buildings as well as large terrain features such as rocky cliffs or an airship dock. They trade off being separately movable, for increased performance by allowing the engine to track one individual "object" rather than potentially thousands of separate small models, and they gain the ability to have per vertex lighting / coloring data applied to them via a VCL file.

Their actual file format is fairly simple, containing a small header containing a version, the materials needed for each mesh, the actual mesh for each individual mesh or object in the static model, and the collision mesh for the entire static model. This collision mesh isn't actually used at runtime, but is used in generating the TTB file, or tile table, that specifies to the client what portions of the map are walkable, and what are not.

## \*.TTB
TTB is a bit of an odd one, it stands for Tile Table, and is a binary file containing a grid, typically 51200x51200, with a flag for walk-able or not for each one. This is what the clients use to decide where the player can and can not go. What makes it interesting, is that it is implemented at the client level, and isn't tied into the engine at all. SS3D's native solution for walk-ability is real-time collision detection, using the collision mesh contained in the static model along with individual collision meshes for any other map objects, however none of the EyaSoft games make use of it, likely due to performance issues at the time.

Even more interesting, is that there are no tools capable of generating or editing these files, in a format that matches what is already present with the clients. The map editor does in theory contain code for generating TTB files, but lacks the ability to set the walk / no walk flag, and is limited to a smaller grid size than required by the client. This suggests there is either a newer version of the map editor that has not been leaked at any point, or perhaps even a separate tile table specific tool. 

## \*.VCH, \*.VCL
These two file types both serve the same purpose but apply to different parts of the map. They specify per vertex color / lighting information, VCH applies to the HeightField, or actual map terrain, and VCL applies to the maps static model.
