# BootIt Bare Metal — Feature Research

> Source: terabyteunlimited.com product pages, user manual (rev 2026-05-16), knowledge base, Vogons community discussions
> Date: 2026-07-09

## What It Is

BootIt Bare Metal (BootIt BM) by TeraByte Unlimited is a boot manager + partition manager + disk imaging tool in one. BIOS/legacy boot only — not for UEFI systems. Current version: 2.12 (June 26, 2026). The BootIt product line has existed since 1994.

## System Requirements

- IBM-compatible PC
- i80386+ CPU
- 16MB RAM
- VGA
- Floppy, CD/DVD, or USB flash drive for boot media
- BIOS-accessible hard drive

## Three Tools in One

### 1. Partition Manager
- Create, move, copy, delete, undelete, resize partitions
- Supports MBR, EMBR (Extended MBR — TeraByte's spec for >4 primary partitions), GPT
- Non-destructive resize of FAT, FAT32, exFAT, NTFS, Linux Ext2/3/4
- 200+ primary partitions on EMBR disks
- Convert between MBR, EMBR, GPT
- Direct PATA/SATA (AHCI) support
- Edit text files on any supported partition, even hidden ones
- Edit Windows BCD store directly (no Windows DVD needed)

### 2. Boot Manager
- Boot any partition on any drive (up to 16 drives)
- Boot multiple OSes from a single partition (Multi-OS feature)
- Hide/unhide partitions PER boot item — each OS sees only what you configure
- Drive swapping (make second hard drive look like the first)
- Floppy A:/B: swapping on the fly
- Auto-detects existing OSes on install
- Direct Boot Menu to bypass normal menu
- Password protection for boot items
- Supports DOS, Windows 9x/ME, NT/2000/XP/2003/Vista/7/8/10, Linux, OS/2

### 3. Disk Imaging
- Full version of Image for DOS included
- Create/restore images of partitions or entire drives
- USB 2.0, IEEE 1394, eSATA support
- Clone/migrate to new drives

## Linux Boot Loader Integration

BootIt BM cannot boot Linux directly. It chainloads to LILO or GRUB installed in the boot sector of a Linux partition (NOT the MBR — BootIt BM owns the MBR). Key rules:
- Install LILO/GRUB to the root partition's boot sector, not /dev/hda (MBR)
- Configure the BootIt BM boot item to point at the partition where the bootloader is installed
- During Linux install, answer "No" to "install bootloader to MBR" and specify the root partition instead

## EMBR Mode

EMBR (Extended Master Boot Record) is TeraByte's proprietary spec for >4 primary partitions on an MBR drive. Standard MBR = 4 primary max. EMBR allows 200+.

Caveat: Once EMBR is enabled, you must use ONLY BootIt BM for all partitioning on that drive. Using FDISK, Windows Disk Management, DISKPART, or any other tool risks data corruption.

## Installation Process

### Step A — Create boot media
Run the MakeDisk utility (on any Windows machine) to create a bootable floppy, CD/DVD, or USB flash drive.

### Step B — Install to hard disk
Boot the retro PC from setup media:
1. "Enable more than 4 primary partitions?" → Yes for EMBR mode, No for standard MBR
2. Choose install partition (auto or manual)
3. Install to its own partition? → Yes recommended (takes ~8MB, type 0xDF)
4. BootIt BM replaces the MBR with its boot manager
5. Auto-detects existing OSes and adds to boot menu

## Pricing

Sold as part of BootIt Collection (BootIt BM + BootIt UEFI). 30-day free trial available (fully functional). All sales final after purchase — the trial is the evaluation period.

## Community Reception

- Vogons (premier retro computing forum): Recommended by users for multi-boot retro builds
- Retrocomputing Stack Exchange: Recommended alongside Ranish Partition Manager
- Key advantage over GRUB4DOS / Plop: partition hiding is per-boot-item and automatic, not manual

## Alternatives

| Tool | Partition Mgr | Boot Mgr | Imaging | Per-Item Hiding | Cost |
|------|:---:|:---:|:---:|:---:|:---:|
| BootIt BM | Yes | Yes | Yes | Yes | Paid |
| GRUB4DOS | No | Yes | No | Manual | Free |
| Plop Boot Manager | No | Yes | No | Manual | Free |
| Ranish Partition Manager | Yes | Yes | No | Yes | Free |
| FDISK active switch | Yes | No | No | No | Free |

BootIt BM is the only tool combining all three functions with automated per-boot-item partition hiding.