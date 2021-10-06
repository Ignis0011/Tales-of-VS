# Tales of VS (Versus)

An attempt to create an English patch for Tales of VS (Versus).

![ToVS](https://raw.githubusercontent.com/Ignis0011/Tales-of-VS/main/proj_logo.png)

Discord: https://discord.gg/tmDgBDNPpE

Spreadsheet: https://docs.google.com/spreadsheets/d/1fYuy52kEaBRSI5-GOqvuJNegd3w4O75ixesAI7vPAiA/edit?usp=sharing

## Hacker Note 1
1. Any and all strings are stored in `\*.msd` files and UTF-16-LE encoded.
2. Format are as follows;

```
Header:
u32 magic "MSDA"
u32 version "\x00\x00\x01\x00"
u32 enum 

Table:
u32 id
u32 flags
u32 toffset
u32 tsize 
```

## Hacker Note 2
1. Some archives are encrypted with zlib, mainly `\*.mgz` files.
2. Format are as follows;
```
u32 magic "SIZE"
u32 usize
bytes zlib "compressed data"
```

## Hacker Note 3
1. MGZ files are commonly used on `*.chain` files that contains `*.rssa` files.
2. Format are as follows;
```
Header:
u32 enum

Table:
u32 foffset
u32 fsize
```

## Hacker Note 4
1. RSSA files contains `*.tm2` files which are essentialy PSP `*.gim` and are images/textures. ssad's are "map" files that track the proper position of images when rendered.
2. I won't be doing much to these, as using TextER is easier, and hopefully the resulting replacements are lesser in size than the original, else a proper tool will be made -_-.
3. Format are as follows;
```
Header:
u32 magic "RSSA"
str fname "40-byte file name"
u16 ssad_enum
u16 tm2_enum
```

## Hacker Note 5 - Text Stuff - Various Routines
1. 0x088C0C5C is what copies text from wherever the text is originally loaded from rom into the ram location the prev function uses, basically a memcpy. Likely not important but here for reference. If we were to change the game to use ASCII over UTF-16, this is one place we'd make those adjustments. (see all the addiu +2/-2's) I do not think that is necessary, though.
```
a0: source address
a1: destination address
a2: size (in # of utf-16 chars)
```
2. 0x088C0FE4 seems to handle creating the texture with the text. (And possibly more?)
```
a3: "destination address" from function #1, where it's reading the text from
```
3. 0x0880A730 writes the actual texture into ram.
```
a0: address to write texture to
a2: # of bytes to write
```
4. 0x088c1bb4 seems to handle generating the GE instructions for printing text
* 0x0880fd7c inside of it writes the instruction - t3 at 0x0880fdd0 sets the texture width. A is 1024, 9 is 512. Probably not important.
* Around 0x088c22d0 (maybe a little bit sooner) are the floating point instructions that generate the values for the vertices for the rectangle draws.

See below for reference:

![image](https://user-images.githubusercontent.com/6155506/136289026-8ae9c8c1-a28b-4150-9d33-3bbfcb64ea17.png)


## Credits
Thanks to `Tales of ABCDE` for hosting the project  
Thanks to `Fistinguranus / Pegi` and `Bugs` for all the structuralization, support, and preliminary translations.

## Third-Party Tools
http://www.romhacking.net/utilities/659/
