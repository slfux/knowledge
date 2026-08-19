---
title: MD5 Hash
---

#cs #crypto #hash


```
// : All variables are unsigned 32 bit and wrap modulo 2^32 when calculating
var int s[64], K[64]
var int i

// s specifies the per-round shift amounts
s[ 0..15] := { 7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22 }
s[16..31] := { 5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20 }
s[32..47] := { 4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23 }
s[48..63] := { 6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21 }

// Use binary integer part of the sines of integers (Radians) as constants:
for i from 0 to 63 do
    K[i] := floor(2^32 × abs(sin(i + 1)))
end for
// (Or just use the following precomputed table):
K[ 0.. 3] := { 0xd76aa478, 0xe8c7b756, 0x242070db, 0xc1bdceee }
K[ 4.. 7] := { 0xf57c0faf, 0x4787c62a, 0xa8304613, 0xfd469501 }
K[ 8..11] := { 0x698098d8, 0x8b44f7af, 0xffff5bb1, 0x895cd7be }
K[12..15] := { 0x6b901122, 0xfd987193, 0xa679438e, 0x49b40821 }
K[16..19] := { 0xf61e2562, 0xc040b340, 0x265e5a51, 0xe9b6c7aa }
K[20..23] := { 0xd62f105d, 0x02441453, 0xd8a1e681, 0xe7d3fbc8 }
K[24..27] := { 0x21e1cde6, 0xc33707d6, 0xf4d50d87, 0x455a14ed }
K[28..31] := { 0xa9e3e905, 0xfcefa3f8, 0x676f02d9, 0x8d2a4c8a }
K[32..35] := { 0xfffa3942, 0x8771f681, 0x6d9d6122, 0xfde5380c }
K[36..39] := { 0xa4beea44, 0x4bdecfa9, 0xf6bb4b60, 0xbebfbc70 }
K[40..43] := { 0x289b7ec6, 0xeaa127fa, 0xd4ef3085, 0x04881d05 }
K[44..47] := { 0xd9d4d039, 0xe6db99e5, 0x1fa27cf8, 0xc4ac5665 }
K[48..51] := { 0xf4292244, 0x432aff97, 0xab9423a7, 0xfc93a039 }
K[52..55] := { 0x655b59c3, 0x8f0ccc92, 0xffeff47d, 0x85845dd1 }
K[56..59] := { 0x6fa87e4f, 0xfe2ce6e0, 0xa3014314, 0x4e0811a1 }
K[60..63] := { 0xf7537e82, 0xbd3af235, 0x2ad7d2bb, 0xeb86d391 }

// Initialize variables:
var int a0 := 0x67452301   // A
var int b0 := 0xefcdab89   // B
var int c0 := 0x98badcfe   // C
var int d0 := 0x10325476   // D

// Pre-processing: adding a single 1 bit
append "1" bit to message<    
 // Notice: the input bytes are considered as bit strings,
 //  where the first bit is the most significant bit of the byte.[52]

// Pre-processing: padding with zeros
append "0" bit until message length in bits ≡ 448 (mod 512)

// Notice: the two padding steps above are implemented in a simpler way
  //  in implementations that only work with complete bytes: append 0x80
  //  and pad with 0x00 bytes so that the message length in bytes ≡ 56 (mod 64).

append original length in bits mod 264 to message

// Process the message in successive 512-bit chunks:
for each 512-bit chunk of padded message do
    break chunk into sixteen 32-bit words M[j], 0 ≤ j ≤ 15
    // Initialize hash value for this chunk:
    var int A := a0
    var int B := b0
    var int C := c0
    var int D := d0
    // Main loop:
    for i from 0 to 63 do
        var int F, g
        if 0 ≤ i ≤ 15 then
            F := (B and C) or ((not B) and D)
            g := i
        else if 16 ≤ i ≤ 31 then
            F := (D and B) or ((not D) and C)
            g := (5×i + 1) mod 16
        else if 32 ≤ i ≤ 47 then
            F := B xor C xor D
            g := (3×i + 5) mod 16
        else if 48 ≤ i ≤ 63 then
            F := C xor (B or (not D))
            g := (7×i) mod 16
        // Be wary of the below definitions of a,b,c,d
        F := F + A + K[i] + M[g]  // M[g] must be a 32-bit block
        A := D
        D := C
        C := B
        B := B + leftrotate(F, s[i])
    end for
    // Add this chunk's hash to result so far:
    a0 := a0 + A
    b0 := b0 + B
    c0 := c0 + C
    d0 := d0 + D
end for

var char digest[16] := a0 append b0 append c0 append d0 // (Output is in little-endian)
```

```c
/* MD5 Library (c) 2025 Steven-L. Fuchs (s.fx) */

#include <stdint.h>
#include <stdlib.h>
#include <string.h>
#include <limits.h>

#include "md5.h"


/* check if underlying platform supports correct size
 * standard defines: sizeof(char) == 1, i.e. char is always 1 byte
 * so if CHAR_BIT = 8, that means: sizeof(uint8_t) == 1
 */
#if CHAR_BIT != 8
#error "Byte not 8 bit in size."
#endif


static const uint8_t s[64] = {
  7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22,
  5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20,
  4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23,
  6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21
};

static const uint32_t K[64] = {
  0xd76aa478, 0xe8c7b756, 0x242070db, 0xc1bdceee,
  0xf57c0faf, 0x4787c62a, 0xa8304613, 0xfd469501,
  0x698098d8, 0x8b44f7af, 0xffff5bb1, 0x895cd7be,
  0x6b901122, 0xfd987193, 0xa679438e, 0x49b40821,
  0xf61e2562, 0xc040b340, 0x265e5a51, 0xe9b6c7aa,
  0xd62f105d, 0x02441453, 0xd8a1e681, 0xe7d3fbc8,
  0x21e1cde6, 0xc33707d6, 0xf4d50d87, 0x455a14ed,
  0xa9e3e905, 0xfcefa3f8, 0x676f02d9, 0x8d2a4c8a,
  0xfffa3942, 0x8771f681, 0x6d9d6122, 0xfde5380c,
  0xa4beea44, 0x4bdecfa9, 0xf6bb4b60, 0xbebfbc70,
  0x289b7ec6, 0xeaa127fa, 0xd4ef3085, 0x04881d05,
  0xd9d4d039, 0xe6db99e5, 0x1fa27cf8, 0xc4ac5665,
  0xf4292244, 0x432aff97, 0xab9423a7, 0xfc93a039,
  0x655b59c3, 0x8f0ccc92, 0xffeff47d, 0x85845dd1,
  0x6fa87e4f, 0xfe2ce6e0, 0xa3014314, 0x4e0811a1,
  0xf7537e82, 0xbd3af235, 0x2ad7d2bb, 0xeb86d391
};


uint8_t *md5Digest(const uint8_t *buf, size_t bufSize, uint8_t res[16]) {
  size_t   i, j, g;
  size_t   newSize;
  uint8_t *chunks;
  uint32_t state[4];
  uint32_t A, B, C, D, F, M;

  state[0] = 0x67452301; // A
  state[1] = 0xefcdab89; // B
  state[2] = 0x98badcfe; // C
  state[3] = 0x10325476; // D

  newSize = bufSize + (64 - bufSize % 64);
  if (bufSize % 64 > 55) newSize += 64;

  chunks = calloc(newSize, 1);
  memcpy(chunks, buf, bufSize);

  chunks[bufSize] = 0x80;

  // write every byte in little endian order into last 8 bytes
  // for every byte:
  //   (message length in bits >> 1 byte) & LSB
  //   ((bufSize * 8) >> (i * 8)) & 0xFF;
  // TODO: mod 2^64
  // TODO: overflow checks, i.e. bufSize << 3 could exceed domain of size_t
  for (i = 0; i < 8; i++)
    chunks[newSize - 8 + i] = ((bufSize << 3) >> (i << 3)) & 0xFF;

  // newSize / 64
  for (i = 0; i < newSize >> 6; i++) {
    A = state[0];
    B = state[1];
    C = state[2];
    D = state[3];

    for (j = 0; j < 64; j++) {

      if (j <= 15) {
        F = (B & C) | ((~B) & D);
        g = j;
      } else if (16 <= j && j <= 31) {
        F = (D & B) | ((~D) & C);
        g = (5 * j + 1) % 16;
      } else if (32 <= j && j <= 47) {
        F = B ^ C ^ D;
        g = (3 * j + 5) % 16;
      } else {
        F = C ^ (B | (~D));
        g = (7 * j) % 16;
      }

      // (i * 64) + (g * 4)
      M = chunks[(i << 6) + (g << 2)]
          | (chunks[(i << 6) + (g << 2) + 1] << 8)
          | (chunks[(i << 6) + (g << 2) + 2] << 16)
          | (chunks[(i << 6) + (g << 2) + 3] << 24);

      F += A + K[j] + M;
      A  = D;
      D  = C;
      C  = B;
      B += (F << s[j]) | (F >> (32 - s[j]));
    }

    state[0] += A;
    state[1] += B;
    state[2] += C;
    state[3] += D;
  }

  for (i = 0; i < 4; i++) {
    res[(i << 2)]     = state[i]         & 0xFF;
    res[(i << 2) + 1] = (state[i] >> 8)  & 0xFF;
    res[(i << 2) + 2] = (state[i] >> 16) & 0xFF;
    res[(i << 2) + 3] = (state[i] >> 24) & 0xFF;
  }

  free(chunks);

  return res;
}

```
