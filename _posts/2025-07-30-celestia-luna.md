---
layout: post
title:  "Celestia LUNA: The Black Sheep"
date:   2025-07-30 14:27:00 -0300
categories: eyasoft ss3d luna-clients
modified_date: 2025-07-31 14:13:00 -0300
last_modified_at: 2025-07-31 14:13:00 -0300
---
If you've been reading along post by post, you may have noticed a certain fairly well known LUNA private server is missing from both the [PAK]({% post_url 2025-06-29-pak-files%}) and [BIN]({% post_url 2025-07-06-bin-files%}) file posts. That server, Celestia LUNA, is just different enough, and with just enough history behind it, that it deserves its own post and some closer examination.

Celestia LUNA has been around for a very long time, it was one of, if not the first LUNA private server, and certainly the first to find any real lasting success, rather than fading into oblivion after a few months like most do. Although most know it as Celestia LUNA, it originally launched under another name, Luna Online Canada some time in early 2009. The earliest archived version of the website is from May 13, 2009, located [here](https://web.archive.org/web/20090513020555/http://lunaonline.ca:80/), says that they are happy to announce the start of their first beta, indicating the actual start of the server was likely not much earlier than that date. The server then announced it had changed its name from Luna Online Canada to the Celestia LUNA we have all come to know as of July 16, 2009, as seen on [this](https://web.archive.org/web/20090720201607/http://celestialuna.com/) copy of its homepage archived shortly after the announcement was made.

If you're looking at those dates and wondering, "How is that possible? Where did they get the code from?". You're thinking the same thing most people thought at the time. LUNA was still officially alive and well at the time, and the appearance of a remarkably stable & feature-complete private server shocked many. What few private servers did exist were based on leaked binary only copies of the server code, ripped from insecure publisher servers rather than from EyaSoft themselves, with mismatched clients and cobbled together databases, they were a buggy, largely unenjoyable experience for most, and all were very short lived. This meant that Celestia was a hot topic of discussion on the official servers, to the point that it was added to the in game chat filter to prevent anyone from directly mentioning it.

Although 2009 did see some copies of server binaries leaked out, and a few less than stellar private servers pop up, it was clear to most that Celestia must have had access to the code to do what they had done, so quickly. Rumors about one of the EyaSoft developers selling a copy of the code to Celestia, without EyaSoft's permission spread. The lack of successful action on EyaSoft's part to have Celestia shut down, whether via legal threat or otherwise, gave some credit to the theory that EyaSoft themselves had a leak they wanted to keep quiet. While the actual truth of exactly where the code came from, how, and how much it may have cost them, is likely only known to the Celestia LUNA developers at this point, and I doubt they have any reason to change that.

What we do know now however, is that there was in fact a copy of the LUNA Online source code for sale in 2009, passed around behind largely closed doors on Chinese forums and websites dedicated to selling leaked and stolen code bases. Whether this is where Celestia obtained their code, or if perhaps their source later spread the code around themselves is not known. Even the existence of this LUNA Online code being out there was not really publicly discussed until years later, long after the LUNA Plus code base had been leaked out to the public in its entirety, originating from these same Chinese forums. The copy of LUNA Online's code in question was from a very early version, roughly matching up to the feature set that Celestia had at release, adding more weight to the theory that this is the same code they started with, but as I said earlier, the exact truth may never be known.

It was this fact, of having seemingly obtained access to a code base many would have paid a lot for, if only they had known it was available, that this post's title originates from. Celestia LUNA became THE server to beat, the one everyone tried to imitate, or in many cases, directly copy. Some quietly resented Celestia LUNA for not releasing the code for the larger community to benefit from. As their popularity grew, so did the number of people trying to rip off their work. This resulted in Celestia going to fairly great lengths to protect their clients, with varying degrees of success. Earlier versions used a product called MoleBox to protect the main client EXE and the engine files from tampering or inspection, while later versions use Themida for the same purpose. Later versions of Celestia, from approximately mid 2014 onward, also encrypt both their BIN and PAK files, although unlike essentially every single other LUNA server, they didn't use EyaSoft's AES implementation because after all, their source predated it, how could they? Instead, they came up with their own solutions, with both PAK and BIN files being encrypted.

Both the BIN and PAK files are encrypted with xSalsa20Poly1305, likely using the NaCL library, although potentially using LibSodium instead, the latter would have been quite new at the time this encryption was introduced, making it possible but less likely. The BIN files are also compressed using LZ4 before they are encrypted, massively decreasing the on disk file sizes of all the BIN files, allowing Celestia to be one of the most compact clients around. This combination of compression, followed by encryption, contained within a Themida protected client, results in a very difficult time for anyone trying to reverse engineer what is going on.

The PAK and BIN files both share the same xSalsa20Poly1305 key. It likely would have been a better idea on Celestia's part to use a different key for each file type, to raise the bar slightly by making anyone attempting to reverse engineer the client and find the key, have to do so twice. Once the use of xSalsa20Poly1305 has been identified, and you've realized that essentially means they must be using NaCL or LibSodium, the only major libraries defaulting to that particular combo, makes finding the key fairly simple, as we know the function signature of the decryption function, letting you locate the key with IDA fairly simply. The decryption key below, is a 32 byte array of unsigned chars (as required by NaCL), despite being chars it does not appear to be valid text, and was likely randomly generated initially. It also does not contain a null terminator and as such should not be handled by anything expecting an actual valid char array.

```cpp
unsigned char celestia_key[32] = {
	0x6C, 0xBD, 0x98, 0x4C, 0xD0, 0x28, 0x3E, 0x4C,
	0x8B, 0x28, 0x47, 0xCD, 0xCB, 0xB0, 0xB4, 0xDA,
	0xDF, 0xB2, 0x5D, 0xAD, 0x1E, 0x24, 0xE4, 0xB3,
	0x5D, 0xC9, 0x42, 0x24, 0xB5, 0xCE, 0x59, 0x5B };
```

## \*.BIN Files
Celestia's BIN file format looks the same as any other at first glance, although anyone trying to decrypt it with the standard EyaSoft BIN code is going to have a very bad time. Its apparent layout, shown bellow, is the same as a normal BIN file. The only difference at first glance is their BIN version, `20140615` not being one of the standard EyaSoft versions. It does appear to be date based like the originals, giving us an idea as to when they began using this protection system.

```cpp
struct MHFILE
{
	uint32_t dwVersion;
	uint32_t dwType;
	uint32_t dwDataSize;

	uint8_t CRC1;
	
	uint8_t FileData[dwDataSize+1];
	
	uint8_t CRC2;
};
```

After the header itself is where things stop being normal and the changes become apparent. `FileData[dwDataSize+1]` actually contains what NaCL (or LibSodium) call a `crypto_secretbox`, which consists of a 24 byte NONCE (a randomly generated "use once" value used in the encryption), followed by a 16 byte MAC (message authentication code, used to detect tampering with the encrypted data), and then the actual encrypted file contents. The following pseudocode is provided to help visualize this structure.

```cpp
unsigned char nonce[24];
unsigned char mac[16];
unsigned char data[dwDataSize - 24 - 16];
```

Upon passing the nonce and ciphertext (the MAC and encrypted data combined together, with the MAC as the first 16 bytes) to NaCL (or LibSodium), along with the decryption key, we end up with... A very garbled looking BIN file. Although the decrypted output will contain bits of readable strings, it's also clearly not correct either. At this point, you might be thinking you've done something wrong with the decryption, but in reality, the payload we decrypted contains compressed LZ4 blocks, along with a small bit of metadata required to decompress them. The first 4 bytes of our decrypted payload contain a `uint32_t` containing the "final" decompressed output size, we need to know this in advance to allow allocating a buffer big enough to hold the decompressed contents.

After decryption and decompression, we finally have our plain text contents, and can read from them like normal. The size of the decompressed text is typically much larger than the size of the BIN file itself, as an example, itemlist.bin in a recent Celestia client is 566kb on disk, but its text contents are 5223kb after decryption and decompression. Although this decryption and decompression does have a relatively high runtime cost, most BIN files are only accessed once at client startup, or upon map changes, so other than slightly longer load times, there isn't much of an actual performance loss in game, making the space savings worth the trade off.

Reverse engineering Celestia was a team effort, despite taking numerous cracks at it myself over the years, I was never able to figure out what was going on myself, largely due to the (wrong) assumption that they would be using the normal AESFile class, backported from the leaked LUNA Plus sources. This made sense to me at the time, because why would they not add in the ready to go encryption system, maybe tweak it a bit, and be done? Upon not finding this, I would end up giving up on it for a while, not knowing where next to look. I asked/begged a good friend, who I'm going to refer to as Porky, for him to take a look at it, and a week or so later we had the first files decrypted and mostly decompressed. I wouldn't be able to write this article without all the help he gave, so thank you very much Porko.

Interestingly, when we reverse engineered Celestia's BIN files, the use of xSalsa20Poly1305 was fairly quickly identified due to the presence of unique strings needed for xSalsa20, however the LZ4 part took MUCH longer to figure out. The original attempt at decoding the BIN files took the form of a python script, written by my friend as my python skills amount to knowing that pythons are snakes. This python script used NaCL to decrypt the BIN contents, and then a rudimentary, black box implementation of LZ4 to decompress the decrypted contents. Of course, we did not know it was LZ4, the python code attempted to match what we could observe from IDA de-compilation and debugging, dumping memory between iterations of the decoding loop to see what changes were made. Surprisingly enough, it did work, most of the time.

Months later, while trying to convert the python into a native C / C++ version (that I could actually understand) for integration into existing BIN viewing / conversion tools, it became clear that this MUST be an actual compression library, as a real implementation was just far to complex for Celestia to have created from scratch just to make BIN files a bit smaller. ChatGPT was consulted, and after giving it the python script as reference, it suggested that it appeared to be LZ77 or LZ4 based, closely resembling the LZ4 block format, if not for the decompressed size being stored immediately in front of the compressed data. Further research revealed this to be a fairly common thing to do, with the python LZ4 library, among other implementations, actually defaulting to storing the size in this exact location, a seemingly quite common solution to the fact that the LZ4 block format does not actually contain the decompressed size, despite requiring it for decompression. A quick library integration later revealed this was indeed the case, and that we had inadvertently written a mostly accurate LZ4 decompressor in python by accident.

## \*.PAK
Celestia's PAK files follow a similar pattern to the BIN files, in that at the surface, they look just like normal EyaSoft PAK files, with all the expected headers in the normal places.

```cpp
struct FSFILE_HEADER
{
	uint32_t dwTotalSize;
	uint32_t dwRealFileSize;
	uint32_t dwFileNameLen;
	uint32_t dwFileDataOffset;
	uint32_t dwFlag[4];

	char szFileName[4];
};
```

Interestingly, some of Celestia's PAK files will extract just fine with the normal EyaSoft PAK tool, but some won't. This is because only some PAKs, and only some files within those PAKs, are encrypted, while the majority are not. It appears any custom made assets are encrypted, while the commonly available original EyaSoft ones are left in the clear to avoid the rather significant performance impact of having to decrypt every texture and model every time it is needed. If a file is unencrypted, it follows the standard EyaSoft format exactly, however if it is encrypted, things get changed up a bit.

Celestia makes use of the normally unused `dwFlag[4]` field in the header, specifically the first field of that array to determine whether a particular file is encrypted, containing either a 0 like a normal EyaSoft PAK for unencrypted or a 1 to indicate an encrypted file. In the case of `dwFlag[0] == 1`, several members of the header become slightly inaccurate, because `dwTotalSize` and `dwRealFileSize` both contain the size of the encrypted file itself, but do not include the extra metadata (nonce and mac) needed for decryption. These two fields add up to an additional 40 bytes of data we need to read that aren't included in the size fields in the header. This means the normal EyaSoft PAK editor will choke on any PAK containing an encrypted file, as it reads the PAK by reading the header, and then seeking forward by the amount of `dwRealFileSize`, but since that is 40 bytes short of the actual payload size, it won't arrive at the next file's header, resulting in an error and aborting.

This all adds up to each file in the PAK looking roughly like the following pseudocode, with the nonce and mac fields only existing when `dwFlag[0] == 1` is set, and their extra 40 bytes needing to be read only in that case.

```cpp
struct FSFILE_CELESTIA
{
	uint32_t dwTotalSize;
	uint32_t dwRealFileSize;
	uint32_t dwFileNameLen;
	uint32_t dwFileDataOffset;
	uint32_t dwFlag[4];

	char szFileName[dwFileNameLen+1];
	
	if(dwFlag[0] == 1)
	{
		uint8_t nonce[24];
		uint8_t MAC[16];
	}
	
	uint8_t fileData[dwRealFileSize];
};
```

That pretty much wraps up the world of Celestia for now, although this post may be expanded in the future if any other interesting details get stumbled upon. Thanks again to my good friend McPorker for all his help figuring out the details on this one.
