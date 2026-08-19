---
title: MBR & VBR
description: Master Boot Record and Volume Boot Record
tags:
- cs
- storage
---

## Volume Boot Record (VBR)

This is the first sector of a partition or the first sector of a storage device if un-partitioned (e.g. floppy).

It contains the [Bios Parameter Block](bpb) and some bootloader code.

The general structure is:

| Field | Description |
|---|---|---|
| Jump code      | Short jump (`0xeb` followed by offset) and a `NOP` (`0x90`). 3 bytes in total. |
| [BPB/EBPB](bpb)| Bios Parameter Block which describes the geometry of the device and the volume. |
| Boot code      | Bootloader code that is jumped to by the first jump instruction. Fills the sector up to the boot signature. |
| Boot signature | `0xaa55` (stored as `0x55`, `0xaa` because of [little endian](endianness.md)) at the very end of the sector. Even though it might not get checked by the BIOS on a partitioned disk it still might be required by the OS bootloader or other tools. |

## Master Boot Record (MBR)