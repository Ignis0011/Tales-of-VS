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

## Hacker Note 6 - Text Limitations, Notes, and Ideas
Currently, if more than 34 characters are in a line, the texture becomes too big for hardware. E width, 34 * 0xE = 510. 35th character takes that to 525. That's larger than 512 and thus increases the texture size to 1024. PSP hardware can't handle textures larger than 512, so characters after 34 repeat from the start. This is not an issue in PPSSPP because it supports textures larger than 512.

There are a couple interesting things in the way the texture is built. Glyphs are aligned to the top, so for the game to align them properly, it shifts the y-coordinate down accordingly. See below:

![image](https://user-images.githubusercontent.com/6155506/136292348-ce02bed3-b782-4121-8cc9-fe9ec6313273.png)

Note how on the bottom line (forced to be 256 wide to cause more wavy text), the wavy effect is because the game is correctly calculating the height of the character that *should* be there. It's easiest to spot when the original line has a space. The space seems to just be blank and forces a full height, so the text looks like it's shifted down to the second line. This is a good thing, so we don't need to worry about that, since it's already handled!

The other interesting thing, and a potential solution around this text limitation, is that the texture is written with 0xE wide characters, but the font is actually 0x7 wide. See below:

![image](https://user-images.githubusercontent.com/6155506/136290739-164fea58-ad04-4d57-ad7e-aa7706871d61.png)

X/Y are coordinates on the PSP, U/V are coordinates on the texture.

The first write writes the full 0xE width texture (0x1 to 0xF on the texture, to 0x80 to 0x8E on the PSP). The second write subtracts 0x7 from the ending x-coord of 0x8E and starts at 0x87 instead, effectively writing over the second half of the first texture, which is probably blank. This means there's a ton of whitespace (half the width, actually) in the texture. 

This means one potential solution, that would double the available text space, is to find how the texture is being drawn, and have it mimic the GE code where it will subtract the starting position for the next glyph so that they are drawn without the blank space, and then edit the GE code so that instead of accessing the full 0xE width of the character from the texture, it only picks up 0x7.

## Credits
Thanks to `Tales of ABCDE` for hosting the project  
Thanks to `Fistinguranus / Pegi` and `Bugs` for all the structuralization, support, and preliminary translations.

## Third-Party Tools
http://www.romhacking.net/utilities/659/
