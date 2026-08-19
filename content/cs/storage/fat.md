---
title: FAT
description: File Allocation Table
tags:
- cs
- storage
---

#cs #storage

The important thing to remember is that **the cluster number stored in a directory entry is not a sector number**. It's an index into the data area of the filesystem.

The process is:

1. Read the starting cluster number from the directory entry.
2. Convert the cluster number to a logical sector number (LBA relative to the volume).
3. Add the partition's starting LBA (or `BPB_HiddSec`) to get the absolute disk LBA.
4. Convert that LBA to CHS if you're using the CHS version of INT 13h.
    

## Step 1: Get the cluster number

Suppose it is

```text
FirstCluster = 5
```

---

## Step 2: Convert cluster → first sector

The first usable cluster is **cluster 2**.

Cluster 0 and 1 are reserved.

The formula is

```text
FirstSector =
    FirstDataSector +
    (FirstCluster - 2) * BPB_SecPerClus
```

where

```text
FirstDataSector =
	ReservedSectors +
	NumberOfFATs * FATSize +
	RootDirSectors    (FAT12/16 only)
```

Example:

```
Reserved sectors = 1
FATs = 2
FAT size = 9 sectors
Root directory = 14 sectors

FirstDataSector = 1 + 18 + 14 = 33
```

Now suppose

```
Cluster = 5
Sectors/cluster = 4
```

Then

```
FirstSector = 33 + (5 - 2) * 4
            = 33 + 12
            = 45
```

So cluster 5 occupies logical sectors

```
45
46
47
48
```

---

## Step 3: Convert volume-relative sector to disk LBA

Suppose the partition begins at LBA 2048.

```
AbsoluteLBA = 2048 + 45
            = 2093
```

---

## Step 4: Convert LBA → CHS

You need the BIOS geometry:

```
HeadsPerCylinder
SectorsPerTrack
```

These can be obtained via INT 13h AH=08h on older BIOSes.

Then

```text
Cylinder = LBA / (Heads * SectorsPerTrack)

Temp = LBA % (Heads * SectorsPerTrack)

Head = Temp / SectorsPerTrack

Sector = (Temp % SectorsPerTrack) + 1
```

Notice the `+1`: sectors in CHS are numbered starting at **1**, not 0.

---

# What if the cluster size is larger than one sector?

Nothing special—you simply read all sectors in the cluster.

For example, if

```
SecPerClus = 8
```

and the cluster starts at LBA 100,

then that cluster consists of

```
100
101
102
103
104
105
106
107
```

When reading a file:

- Compute the first sector of the cluster.
- Read `BPB_SecPerClus` consecutive sectors.
- Look up the next cluster in the FAT.
- Repeat until the FAT indicates end-of-chain.

So a cluster is just a group of consecutive sectors. The FAT links **clusters**, not individual sectors.

## Modern note

If you're writing a bootloader or DOS-compatible filesystem driver, this is the traditional approach. However, if the BIOS supports the INT 13h Extensions (AH=42h), it's usually preferable to use LBA reads directly and skip the CHS conversion entirely. Internally, FAT is naturally addressed in terms of logical sectors (LBAs), and CHS is only needed for the legacy INT 13h interface.


## Resources

- [The Skinny on FAT](http://scottie.20m.com/fat.htm)