---
layout: post
title:  "A Brief History Of EyaSoft & SS3D"
date:   2025-06-10 17:39:00 -0300
categories: eyasoft ss3d
---
One of the primary topics of this blog will be EyaSoft and it's games, as well as the engine powering most of them. In the course of researching information about the games themselves, I've ended up with a fair knowledge of the company and its origins as well. So here is a fairly brief look at the history of EyaSoft and their various games, in case anyone else is interested in where this all started.

EyaSoft was sometimes also known as Eya Interactive Ltd, depending on where you look and when. Eya Interactive Ltd was registered in the UK, while EyaSoft is a Korean company, so it seems this separate entity may have just existed for tax purposes. Fun fact, the EYA in EyaSoft and Eya Interactive stands for "Enjoy Your Adventure", this tagline was featured under some of EyaSoft's logos.

EyaSoft's eventual formation was the result of a chain of different companies mergers and closures in the late 90s and early 2000s. It started out as HICOM in 1988, which merged with another company named FEW in 1999 to become eSofNet. For those interested in more info on these early pre-EyaSoft days, there is an excellent article about HiCom's early history and games here: [Article](http://www.hardcoregaming101.net/korea/part1/company-hicom.htm) ([Archived Version](https://web.archive.org/web/20250217113356/www.hardcoregaming101.net/korea/part1/company-hicom.htm))

It's at eSofNet that the engine known as SS3D was initially created to serve as the base for the new companies direction of focusing on MMO development, at the time a very new, promising market. Development started on two titles using this engine, Corum Online, and Mosiang, which at the time was called Dark Story Online. Both of these titles were released in late 2003, just in time for eSofNet to close in 2004. Corum Online's development team then started NetTimeSoft to continue development of their game, although they seem to have closed some time in 2007. Dark Story Online's development team went on to found the EyaSoft we have come to know and love, to continue the development of Dark Story, now known as Mosiang, and eventually renamed to Titan Online after a 2007 expansion.

EyaSoft, thriving off of Mosiang's early successes, then began creating LUNA Online in 2004, forked directly from Mosiang's code base. LUNA Online was finally released in 2007, to great initial success, and work on it's first major expansion began, which would eventually release as LUNA Plus. It was also at this point in 2007, that the LUNA Online code base would be forked to begin work on what would eventually become Legend of EDDA, which began testing in late 2009 and released in 2010.

Interestingly, despite developing multiple games simultaneously on the same engine, the different teams at EyaSoft didn't collaborate in any detectable fashion. This resulted in bugs in shared systems being fixed in one game, but not the other, and vice-versa. This also resulted in major features being added to one game, and the others never receiving them. For example, Legend of EDDA is the only SS3D game to support WASD movement, all of the other titles only support click target based movement.

To help understand the slightly convoluted timeline of games, the following tree shows a rough timeline of releases for all of the SS3D games and what code base they were based off of.

```
SS3D(2003)
├── Corum Online (2003)
└── Mosiang (2003)
	├── Titan Online (2007)
	└── LUNA Online (2007)
		├── LUNA Plus (2009)
		└── Legend of EDDA (2010)
```

Most of EyaSoft's games, including all of the ones mentioned previously, rely upon an in-house game engine called SuperShovel3D, or SS3D for short. It is entirely based upon DX's fixed function pipeline, and does not make use of any actual shaders directly, instead relying upon DX generating them as needed. This unfortunately does limit performance and upgradability to newer versions of DirectX, while also making working with the engine harder than many newer, modern game engines.

SS3D remained largely unchanged for most of the first half of its lifetime, receiving only minimal changes from its initial usage in Mosiang & Corum, all the way through Titan Online and LUNA Online's release. Midway through LUNA Online's lifetime, the engine was upgraded from DirectX 8, to DirectX 9, and then was further upgraded with several new collision testing functions needed for some of the new features added in LUNA Plus. One major upside of the engine changing so little over its life is that it enables us to get all 6 game code bases running on a single updated version of the engine, with minimal changes needed to each game.

There are several exceptions in EyaSoft's history that don't make use of SS3D, however only two of these were actually released: IRIS Online, and Heroes Of Obelisk, more commonly known as HOTO. These two games make use of a licensed copy of GameBryo, presumably they saw the limitations of their own engine as an issue going forward as it aged and decided to try and pivot off of it for new projects. Sadly, this doesn't seem to have worked, as EyaSoft shut down not long after HOTO's release, after its launch was plagued with complaints about certain design decisions. Somewhat interestingly, HOTO is the only EyaSoft game whose source code has never been leaked publicly.

There is actually a third GameBryo based game as well, however it's an even more special case than the other two. During one of EyaSoft's several periods of financial trouble, they sold a license to an early copy IRIS Online's code base to a Chinese developer, known as mc916, to use as a base to make their own game, but with material more appealing to their target market. This game was known as MHKX, whose actual meaning in English isn't clear, and direct translation of the Chinese name gives you something like "Fantasy Card Online" depending on the translator used.

MHKX was developed from this fork point onward without any EyaSoft involvement, by the Chinese developer only. Very little information about the game, or the publisher, exists on the English internet, making research on this one much harder. Unfortunately, it's code base is of a lower quality, and contains severe bugs that make working with it in a modern compiler difficult. This may have been because of the Chinese developers not being as familiar with the underlying code base as the original IRIS team was. Combined with the difficulty in accurately translating Chinese, this makes working with this one quite difficult.