---
title: Drive Addressing
description: Different Forms of Addressing on Drives
tags:
- cs
- storage
---

#cs #storage 

## Cylinder Head Sector (CHS)

This is an old way of addressing disk drives via the parameters cylinder, head and sector.

![[chs.png]]

## Logical Block Addressing (LBA)

This maps every storage unit (e.g. sectors) onto a linear numbering scheme.

## Conversion Between CHS and LBA

CHS can be mapped to LBA address with the following formula:

> _LBA_ = (_C_ × _HPC_ + _H_) × _SPT_ + (S − 1)

LBA addresses mapped to CHS: 

> _C_ = _LBA_ ÷ (_HPC_ × _SPT_)  
> _H_ = (_LBA_ ÷ _SPT_) mod _HPC_  
> _S_ = (_LBA_ mod _SPT_) + 1  

where

- "÷" is integer division
- _C_, _H_ and _S_ are the cylinder number, the head number, and the sector number
- _LBA_ is the logical block address
- _HPC_ is the maximum number of heads per cylinder
- _SPT_ is the maximum number of sectors per track

## Resources

- [Logical Block Adressing](https://en.wikipedia.org/wiki/Logical_block_addressing)