---
title: BPB
description: Bios Parameter Block
---

#cs #storage #boot

## Synopsis

This stores the geometry of the storage device and information on the volume.
It is contained in the [Volume Boot Record](mbr-vbr).

## Structure

Full structure including EBPB for FAT12/16:

| Size | DOS Version | Field |
|---|---|---|
| db[8]  | 2.0  | OEM string identifying the DOS version. |
| dw     | 2.0  | Bytes per sector. |
| db     | 2.0  | Sectors per cluster. FAT uses clusters to group multiple sectors together. |
| dw     | 2.0  | Reserved sectors starting at VBR. I.e. you can technically have more then one sector for boot. |
| db     | 2.0  | FATs on disk. Usually 2. This is just done for redundancy in case of data loss. |
| dw     | 2.0  | Number of root directory entries. This sets the size of the root directory. **0 for FAT32.** |
| dw     | 2.0  | Total number of sectors on disk. If more than 65535 then set to 0 and use large sector count below.|
| db     | 2.0  | Medium descriptor. Single byte that says what kind of storage device this is. | 
| dw     | 2.0  | Sectors per FAT. I.e. the size of the FAT. **0 on FAT32.** |
| dw     | 3.0  | Sectors per track. |
| dw     | 3.0  | Number of sides/heads. |
| dd     | 3.31 | Number of hidden sectors. I.e. the number of sectors that come before this sector. |
| dd     | 3.31 | Large sector count. For disk partitions having more than 65535 sectors. |
| db     | 3.4  | Drive number. |
| db     | 3.4  | Reserved. Used for flags on Windows NT. |
| db     | 3.4  | EBPB signature. `0x28` for only serial number and `0x29` for next three fields. |
| dd     | 3.4  | Volume serial number. |
| db[11] | 4.0  | Volume label string. Just a name for the volume. |
| db[8]  | 4.0  | File-system type string. E.g. `FAT12` (padded with spaces). |

The BPB with EBPB for FAT32 differs in the last fields:

> [!info]
> The sector and cluster addresses are **logical and relative to the start of the volume**!

| Size | Field |
|---|---|
| --     | DOS 3.31 BPB |
| dd     | Sectors per FAT. |
| dw     | Flags. |
| dw     | FAT Version. High byte = major version, low byte = minor version. |
| dd     | Root directory cluster. |
| dw     | FSInfo sector. |
| dw     | Backup boot sector. |
| db[12] | Reserved. set to 0. |
| db     | Drive number. |
| db     | Reserved. Flags on Windows NT. |
| db     | EBPB signature (`0x29`). |
| dd     | Volume serial number. |
| db[11] | Volume label string. |
| db[8]  | File-system type string. |

## Example

```asm
; boot sector of fat12 formatted disk 
; disk is intended as 3.5" high density double sided
;   2 sides
;   80 tracks per side
;   18 sectors per track
;   512 bytes per sector

org 0x7c00 ; offset to loading address

jmp bootcode ; skip bpb
nop

bpb:
  db "BOOTLD10" ; oem
  dw 512        ; bytes per sector
  db 1          ; sectors per cluster
  dw 1          ; reserved sectors
  db 2          ; tables on disk
  dw 224        ; possible root dir entries
  dw 2880       ; total sectors
  db 0xf0       ; medium descriptor
  dw 9          ; sectors per fat
  dw 18         ; sectors per track
  dw 2          ; number of sides/heads
  dd 0          ; number of hidden sectors
  dd 0          ; large sector count
  db 0          ; drive number
  db 0          ; reserved
  db 0x28       ; ebpb signature
  dd 0xB16B00B5 ; serial number

bootcode:
  jmp bootcode ; halt

times 510-($-$$) db 0 ; padding
dw 0xaa55             ; boot signature
```

## See Also

- [File Allocation Table (FAT)](fat.md)
- [Master Boot Record (MBR) and Volume Boot Record (VBR)](mbr-vbr.md)

## Resources

- [ **All the Details of _many versions of  both_ MBR (Master Boot Records)  _and_ OS Boot Sectors (_also called:_  Volume Boot Records)**](https://thestarman.pcministry.com/asm/mbr/index.html)
- [BIOS Parameter Block](https://en.wikipedia.org/wiki/BIOS_parameter_block)