# Tales of VS (Versus)

An attempt to create an English patch for Tales of VS (Versus).

![ToVS](https://raw.githubusercontent.com/Ignis0011/Tales-of-VS/main/proj_logo.png)

Discord: https://discord.gg/tmDgBDNPpE

Spreadsheet: https://docs.google.com/spreadsheets/d/1fYuy52kEaBRSI5-GOqvuJNegd3w4O75ixesAI7vPAiA/edit?usp=sharing

## Hacker Note 1
1. Any and all strings are stored in `\*.msd` files.
2. Format are as follows;

```
Header:
u32 magic "MSDA"
u32 version "\x00\x00\x01\x00"
u32 enum 

Table:
u32 id
u32 unknown "flags"
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
2. I won't be doing much to these, as using in TextER is easier, and hopefully the resulting replacements are lesser in size than the original.
3. Format are as follows;
```
Header:
u32 magic `RSSA`
str fname `40 bytes`
u16 ssad_enum
u16 tm2_enum
```

## Credits
Thanks to `Tales of ABCDE` for hosting the project
Thanks to `Fistinguranus / Pegi` and `Bugs` for all the structuralization, support, and preliminary translations.

## Tools

http://www.romhacking.net/utilities/659/
