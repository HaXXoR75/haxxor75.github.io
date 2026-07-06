---
layout: post
title:  "SS3D's Missing Links"
date:   2026-07-05 14:58:00 -0300
categories: eyasoft ss3d
---
Today we are going to briefly discuss some portions of SS3D that are hard, or impossible to acquire source code for, making bug fixes and upgrades to them nearly impossible.

If you were going to start working on LUNA Online, or even Legend Of EDDA today, one of your first actions when diving into the source code is almost certain to be upgrading it to a modern compiler, likely Visual Studio 2026 at time of writing, to avoid trying to get Visual Studio 2003 (or 2005 for EDDA) working on a modern version of Windows. There is a large list of things needed to accomplish this, but most of them are fairly straightforward, especially if you're already familiar with the rules of modern C++. 

For the most part, this goes as you would expect: fix any errors, compile with a new version of Visual Studio, start from the base SS3D libraries, and eventually work up to the game itself. When doing this with the publicly available copies of either game's source code, you'll rapidly discover there are two components you simply can't rebuild with a modern compiler, because they aren't in the source code. 

Those components are MHConsole, and IndexGenerator. MHConsole provides the actual console interface used by the game servers, and as such is quite important as without it you cannot run the game servers. IndexGenerator is used in both the game servers, and the clients themselves, and does what its name would suggest, it generates indexes for use by various systems. 

## MHConsole

MHConsole is a strange omission from the leaks, just due to how necessary it is, suggesting it was either stored somewhere entirely separate from the engine and game sources, or that whoever leaked all the sources simply missed including it.

With enough digging, however, there is actually a single publicly available copy of the MHConsole source code, located inside of Mosiang's source leak. It is obviously extremely old, but like the rest of SS3D, changes over the years range from non-existent to minimal, letting you largely drop this copy into the other games with a similar amount of modernization work.

It's interesting that the only copy of it is inside the oldest of Eya's SS3D games, but not in any of the later LUNA or EDDA leaks. Why was this one engine component not located with these game's copies of the engine? It's possible Eya themselves may have lost the code, and may have continued to use their compiled copy simply because it was so entrenched in the game server's designs. This theory is lent more credit by the fact that EDDA uses MHConsole in its earlier iterations, but ditches it for an entirely new system in the later ones. EyaSoft doesn't typically rewrite anything from scratch unless forced to.

## IndexGenerator

IndexGenerator is even stranger, because there is stronger reason to assume EyaSoft lost the code for it entirely. No matter where you look, LUNA, EDDA, further back to Mosiang or Titan online, there isn't any source code. Even resorting to looking in the Corum Online source code, the only non-EyaSoft SS3D game I know of with available source, doesn't help.

Every SS3D-based EyaSoft game used this library, without a single copy of the code for it. Instead they all have an identical, and apparently VC6-compiled static library and header, stashed away with some other older engine bits in the YHLibrary folder. This suggests they weren't able to recompile it when they moved to VS2003 or later to VS2005, lending credit to the theory that they lost, or never had, the code for it.

Why they didn't migrate off of it onto something else is unknown, but the most likely answer is simply that it never broke, so they never bothered. The actual game teams may not have even realized anything was wrong, simply making use of a class the engine made available.

Luckily for LUNA private server developers everywhere, IndexGenerator is quite simple, consisting of only four functions and only making use of Win32 types, such as DWORD, BOOL, etc. This means that despite being absolutely ancient, the library is still linkable with a modern compiler. The code is simple enough that there aren't any ABI violations or conflicts with the ancient C library it could have been making use of.

### Reverse engineering

I originally reverse engineered the library before AI coding agents were commonplace, and instead relied entirely on decompilation, and verifying behavior against the original library. These days, with just the header alone, AI can generate a passable implementation, however the original does have numerous behavioral quirks that need to be reproduced for such an attempt to actually function properly. The more reliable route is to decompile the library yourself, with IDA Pro or Ghidra, allowing you to see just how it was implemented to begin with, and then re-implement it with that knowledge, either by yourself or with AI assistance.

In order to recreate the library, or at least its functionality, we first need to understand how it behaves in use, and what it is supposed to actually do, as well as what the existing library returns as results to various inputs, both valid and invalid. Once we have this information, we can document the behavior of the system, and from that, we can create a replacement that follows the same behavior.

`CIndexGenerator` is, at its heart, a (typically) small fixed-size FIFO pool for DWORD index values. You initialize it with a contiguous range of IDs, then ask it for the next available index; when the pool is empty, it returns 0 to indicate failure. When an index is released, the original implementation simply appends it back onto the queue for later reuse. The tradeoff is that the original code does not protect against invalid releases such as 0 or out of range indexes, and it does not detect duplicate releases. If the same index is released twice, both copies end up in the queue, which means the generator will later hand out the same supposedly-unique index more than once.

Actually protecting against that is quite difficult, and requires additional overhead tracking whether an index is eligible to be freed or not. The original implementation doesn't make any attempt to guard against that, and a faithful reproduction doesn't need to either.

The following is a functional replacement for IndexGenerator; combine it with the existing header, create a Visual Studio project to compile it with, and you'll have a drop-in replacement for the original, compiled on a modern compiler, with all observable behavior the same as the original. 

The only deviations from the original's behavior are in the form of additional bounds checking and protection against double initialization. These don't change how the library behaves in normal use, but do make it harder to do things wrong when using it. 
These changes consist of the first two lines of `Init(DWORD dwMaxIndexNum, DWORD startIdx)`, the first line of `GenerateIndex()` and the first two and the fifth line of `ReleaseIndex(DWORD dwIndex)`. The original implementation lacks all of this hardening and will instead crash upon you doing something foolish.

```cpp
#include "IndexGenerator.h"

CIndexGenerator::CIndexGenerator()
	: m_pIndexTable(NULL), front(0), rear(0),
	  m_dwMaxIndexNum(0), m_dwStartIndex(1) {}

CIndexGenerator::~CIndexGenerator()
{
	Release();
}

void CIndexGenerator::Init(DWORD dwMaxIndexNum, DWORD startIdx)
{
	Release();
	if(dwMaxIndexNum == 0 || dwMaxIndexNum == MAXDWORD) return;

	m_dwMaxIndexNum = dwMaxIndexNum;
	m_dwStartIndex = startIdx;
	front = 0;
	rear = 0;

	m_pIndexTable = new DWORD[m_dwMaxIndexNum + 1];
	for (DWORD i = 0; i <= m_dwMaxIndexNum; ++i)
	{
		m_pIndexTable[i] = 0;
	}

	for (DWORD i = 0; i < m_dwMaxIndexNum; ++i)
	{
		m_pIndexTable[rear] = m_dwStartIndex + i;
		rear = (rear + 1) % (m_dwMaxIndexNum + 1);
	}
}

void CIndexGenerator::Release()
{
	delete[] m_pIndexTable;
	m_pIndexTable = NULL;
	front = 0;
	rear = 0;
	m_dwMaxIndexNum = 0;
	m_dwStartIndex = 1;
}

DWORD CIndexGenerator::GenerateIndex()
{
	if(m_pIndexTable == NULL || m_dwMaxIndexNum == 0 || front == rear) return 0;

	const DWORD index = m_pIndexTable[front];
	m_pIndexTable[front] = 0;
	front = (front + 1) % (m_dwMaxIndexNum + 1);
	return index;
}

BOOL CIndexGenerator::ReleaseIndex(DWORD dwIndex)
{
	if(m_pIndexTable == NULL || m_dwMaxIndexNum == 0 || dwIndex == 0) return FALSE;
	if(dwIndex - m_dwStartIndex >= m_dwMaxIndexNum) return FALSE;

	const DWORD nextRear = (rear + 1) % (m_dwMaxIndexNum + 1);
	if(nextRear == front) return FALSE;

	m_pIndexTable[rear] = dwIndex;
	rear = nextRear;
	return TRUE;
}
```

This code should be quite similar to the original implementation (other than the added hardening), or at least should be, given decompilation doesn't always reproduce the exact code, especially for low-level code like this.

Despite being only 70 or so lines long, and even less without the added error checking, actually verifying the behavior against the original required writing a 600-line test suite to find the rough edges, map how the original handled them, and then make sure our new version responds the same way, or in a more reasonable way, such as returning the invalid 0 index to indicate failure instead of crashing outright.