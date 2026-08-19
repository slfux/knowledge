---
title: GRX
description: Specification of the GRX Font Format
---

#cs #format #font #text

# Synopsis

There is practically no info online about how GRX fonts are structured so i looked at the source code of the GRX library and examined .fnt files inside a hexdump editor.
I thought i'd share what i've found.

# Structure

So first of all, here are the declarations of how the GRX library stores GRX font headers:

```c
/*
 *  font file structure:
 *  +-----------------------+
 *  |     FILE HEADER       |
 *  +-----------------------+
 *  |     PROPORTIONAL      |
 *  |     WIDTH TABLE       |
 *  |     (16 bit ints)     |
 *  |     (prop fonts only) |
 *  +-----------------------+
 *  |     BITMAP            |
 *  +-----------------------+
 */

#define GRX_NAMEWIDTH         16
#define GRX_FONTMAGIC         0x19590214L
#define GRX_FONTMAGIC_SWAPPED 0x14025919L

typedef struct _GR_fontFileHeaderGRX {  /* the header */
  GR_int32u magic;                  /* font file magic number */
  GR_int32u bmpsize;                /* character bitmap size */
  GR_int16u width;                  /* width (average for proportional) */
  GR_int16u height;                 /* font height */
  GR_int16u minchar;                /* lowest character code in font */
  GR_int16u maxchar;                /* highest character code in font */
  GR_int16u isfixed;                /* nonzero if fixed font */
  GR_int16u reserved;               /* ??? */
  GR_int16u baseline;               /* baseline from top of font */
  GR_int16u undwidth;               /* underline width (at bottom) */
  char      fnname[GRX_NAMEWIDTH];  /* font file name (w/o path) */
  char      family[GRX_NAMEWIDTH];  /* font family name */
} GrFontFileHeaderGRX;
```

Here's my more C99-friendly declaration:

```c
typedef struct {
  uint32_t magic;
  uint32_t bmpsize;
  uint16_t width;
  uint16_t height;
  uint16_t minchar;
  uint16_t maxchar;
  uint16_t isfixed;
  uint16_t reserved;
  uint16_t baseline;
  uint16_t undwidth;
  char     fnname[16];
  char     family[16];
} GRX_FONT_HDR;
```

**THE FILE ITSELF STORES THE VALUES IN LITTLE ENDIAN!!!!**  
On big endian machines the GRX library source code swaps the bytes to big endian.

## Fields

Here's the explanation as to what the fields mean:

`uint32_t magic;`

> Magic number/signature of the file.
> Bytes in order: `0x14`, `0x02`, `0x59`, `0x19`.

`uint32_t bmpsize;`

> Size of bitmap in bytes.
> Should equal to file size - (header size + table size).

`uint16_t width;`

> Width of each character in pixels.
> Is set to an average if the font is proportional, i.e. variable width.

`uint16_t height;`

> Height of each character in pixels.

`uint16_t minchar;`

> Minimum character code.
> The codes can be interpreted as you wish but in all fonts it's ASCII.
> Unless there are fonts that i haven't seen which are more fancy.

`uint16_t maxchar;`

> Maximum character code.
> Same thing with the interpretation as `minchar`.

`uint16_t isfixed;`

> Boolean of whether the font is monospaced/fixed width or proportional.

`uint16_t reserved;`

> Unused or padding.
> Is just set to zero.

`uint16_t baseline;`

> Baseline position in pixels measured from the top.
> The baseline is the line on which the symbols sit on ;-)

`uint16_t undwidth;`

> Height of underline in pixels.
> A little confusing naming, i know.

`char fnname[16];`

> Filename.

`char family[16];`

> Font family name.

## Width Table

If the font is proportional then the next thing that follows is a width table.
That width table is just a bunch of `uint16_t` which encode the width for the corresponding character in pixels.
E.g. if you have set `minchar` to 32 (which is space in ASCII) than the first 16 bit of the table encode the width of character 32.
The next 16 bit then would encode the width of character 33 and so on.

## Bitmaps

After that comes the bitmap which encodes each character with bytes as rows of pixels where each bit represents a pixel.
The rows are in scan-line order, i.e. the character is encoded from top to bottom and from left to right.
E.g. the following 5x5 "A" character:

    . . # . .
    . # . # .
    . # # # .
    . # . # . -- this would be the baseline if set to 4
    . . . . .

would be encoded as:

    00100000 n
    01010000 n+1
    01110000 n+2
    01010000 n+3
    00000000 n+4

If the character is more then 8 pixels wide then you use more than one byte per row, i.e. you use $\lceil\frac{\text{width}}{8}\rceil$ number of bytes per row.
The character codes are the same as with the width table.
If you have set `minchar` to a value N then the first $\text{height} \cdot \lceil\frac{\text{width}}{8}\rceil$ bytes encode character N, which is the followed by N+1 etc.