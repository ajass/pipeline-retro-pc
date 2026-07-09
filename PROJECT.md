# PROJECT.md — Pipeline-Retro-PC

**Created:** 2026-07-09
**Status:** Planning — hardware TBD, partition layout and install sequence defined
**Goal:** Multi-boot DOS 6.22, Windows 98 SE, BeOS R5, Windows XP, and Linux from a single drive using BootIt Bare Metal

---

## 1. Operating Systems and Constraints

Each OS has era-specific limitations that constrain partition layout, drive size, and install order.

### DOS 6.22
- Filesystem: FAT16 only (max 2GB per partition)
- Cannot access FAT32 or NTFS
- Cannot access partitions beyond 8GB cylinder boundary
- Must be installed on a primary partition within first 8GB of the drive
- Boot files (IO.SYS, MSDOS.SYS, COMMAND.COM) must be in first clusters of C:

### Windows 98 SE
- Filesystem: FAT16 or FAT32
- 128GB drive size limit (without patched drivers like rloew's)
- Cannot read/write NTFS natively (read-only via third-party drivers)
- Needs a primary partition (or a logical volume with boot files on a FAT16/FAT32 primary)
- Windows 9x boots from C: — its boot files land on the active primary partition
- Supports real-mode DOS for gaming (DOS 7.1 built in)

### BeOS R5 (x86)
- Filesystem: BFS (Be File System) — native, journaling, 64-bit capable
- Min requirements: 32MB RAM, 200MB disk space, Pentium-class CPU
- Max RAM: ~1GB (crashes above this)
- Partition type: 0xEB (BeOS BFS) — BootIt BM supports custom partition types
- Can install its own boot manager (bootman) — but we will use BootIt BM instead, so decline bootman during install
- BeOS Drive Setup can partition and format BFS; alternatively pre-create the partition in BootIt BM and format with 2KB block size
- Recommended partition size: 500MB–2GB (BeOS R5 is small)
- Position constraint: ideally within first 8GB for safest BIOS access (BeOS R5 uses INT 13h)

### Windows XP
- Filesystem: NTFS (recommended) or FAT32
- Can be installed on a primary partition
- Uses NTLDR/BOOT.INI boot mechanism (NT/2000/XP family)
- No cylinder/size limit for modern XP installs with SP2+
- Can read FAT16, FAT32, NTFS

### Linux (period-appropriate distro TBD)
- Filesystem: Ext2/3/4, ReiserFS, etc.
- BootIt BM cannot boot Linux directly — chainloads to LILO or GRUB installed in the Linux partition's boot sector (NOT the MBR)
- Can be on a primary or logical partition
- During install: answer "No" to "install bootloader to MBR" and specify the root partition's boot sector
- Period options: Slackware (period-correct for PII/PIII), Debian 3.x, Red Hat 7-9, or a lightweight modern distro if hardware allows

---

## 2. Hardware Requirements (TBD — needs user input)

The hardware determines what is practical. Key questions:

- **CPU/Motherboard era:** Determines max RAM, drive interface (IDE/PATA vs SATA), and which OSes are comfortable
  - Pentium II/III (Slot 1/Socket 370): ideal for DOS, Win98, BeOS, early Linux, WinXP
  - Pentium 4 (Socket 478): works for all five but DOS/Win98 need care with speed (CPU too fast for some DOS games)
  - Athlon XP (Socket A): same considerations as P4
- **RAM:** 256MB–512MB sweet spot for running all five OSes. BeOS crashes above 1GB. DOS/Win98 do not benefit from >512MB.
- **Storage:** IDE/PATA drive or IDE-to-SD/IDE-to-CF adapter. 40GB–128GB range. Must stay under 128GB for Win98 compatibility.
- **GPU:** Period-correct (Voodoo 3, TNT2, Rage 128, etc.) for Win98 gaming. BeOS has limited driver support — VESA fallback works.
- **Optical drive:** CD-ROM or DVD-ROM for OS installation media. BootIt BM setup media (CD or USB).

---

## 3. Partition Layout (Single Drive)

Requires EMBR mode in BootIt BM (more than 4 primary partitions needed: BootIt partition + 5 OS partitions + 1 data partition = 7 total).

Drive size assumption: 40GB–128GB (TBD based on hardware). Layout shown with example 80GB drive.

| Partition | Type | FS | Size | Purpose | Position Constraint |
|:---:|:---:|:---:|:---:|---|---|
| P0 | EMBRM (0xDF) | — | ~8MB | BootIt BM boot manager | Must be first |
| P1 | Primary | FAT16 | 1GB | DOS 6.22 | Must be within first 8GB |
| P2 | Primary | FAT32 | 10GB | Windows 98 SE | Within 128GB limit |
| P3 | Primary | BFS (0xEB) | 2GB | BeOS R5 | Within first 8GB for safe BIOS access |
| P4 | Primary | NTFS | 20GB | Windows XP | No constraint |
| P5 | Primary | Ext2/3 | 10GB | Linux | LILO/GRUB in boot sector, not MBR |
| P6 | Primary | FAT32 | ~37GB | Shared data | Visible from all OSes except DOS (FAT32) |

### Boot Items in BootIt BM

Each boot item shows only the target OS partition and the shared data partition. All other OS partitions are hidden.

| Boot Menu Item | Boot Partition | Visible Partitions | Hidden Partitions |
|---|:---:|---|---|
| DOS 6.22 | P1 (FAT16) | P1, P6* | P2, P3, P4, P5 |
| Windows 98 SE | P2 (FAT32) | P2, P6 | P1, P3, P4, P5 |
| BeOS R5 | P3 (BFS) | P3, P6** | P1, P2, P4, P5 |
| Windows XP | P4 (NTFS) | P4, P6 | P1, P2, P3, P5 |
| Linux | P5 (Ext2/3) | P5, P6*** | P1, P2, P3, P4 |

*P6 is FAT32 — DOS 6.22 cannot read FAT32. DOS will only see P1 as C:. If a DOS-visible data partition is needed, add a small FAT16 partition (512MB) below the 8GB line.

**BeOS can read FAT32 via its BFS file system add-ons, but BFS is native. P6 visibility optional.

***Linux can read FAT32, NTFS, and Ext2/3. P6 as FAT32 is readable from Linux.

### Optional: DOS-visible data partition

If you want DOS 6.22 to access shared data, add a FAT16 partition (max 2GB) within the first 8GB:

| Partition | Type | FS | Size | Purpose |
|:---:|:---:|:---:|:---:|---|
| P6a | Primary | FAT16 | 2GB | DOS-shared data (within 8GB limit) |
| P6b | Primary | FAT32 | ~35GB | Win98/XP/Linux shared data |

This would make 8 partitions total — still fine with EMBR mode.

---

## 4. Install Sequence

Install oldest OS first, newest last. Each installer tends to overwrite the MBR; BootIt BM can recover but chronological order is cleaner.

### Phase 0: Prep
1. Back up any existing data on the drive (if not blank)
2. Create BootIt BM setup media (floppy/CD/USB) using MakeDisk on a modern machine
3. Create OS installation media:
   - DOS 6.22 boot floppy + install disks
   - Windows 98 SE boot floppy + CD
   - BeOS R5 CD (or boot floppy + CD)
   - Windows XP install CD
   - Linux install CD/floppies (distro-dependent)
4. Set BIOS to boot from the BootIt BM media first

### Phase 1: Install BootIt BM
1. Boot from BootIt BM setup media
2. When asked "enable more than 4 primary partitions?" → **Yes** (EMBR mode)
3. Let setup choose partition, or choose manually
4. Install to its own partition → **Yes** (creates P0, ~8MB, type 0xDF)
5. BootIt BM now owns the MBR

### Phase 2: Create All Partitions
1. Boot into BootIt BM desktop
2. Open Partition Work
3. Create P1: FAT16, 1GB (DOS 6.22)
4. Create P2: FAT32, 10GB (Windows 98 SE)
5. Create P3: BFS/0xEB, 2GB (BeOS R5) — may need to enter partition type manually
6. Create P4: NTFS, 20GB (Windows XP) — format can happen during XP install
7. Create P5: Ext2/3, 10GB (Linux) — format can happen during Linux install
8. Create P6: FAT32, remaining space (shared data)
9. Do NOT format P3, P4, P5 yet — let each OS installer format its own partition to avoid filesystem creation quirks

### Phase 3: Install DOS 6.22 (P1)
1. In BootIt BM, create boot item "DOS 6.22"
   - Boot partition: P1
   - Hide: P2, P3, P4, P5
   - Show: P1, P6
   - One Time Option: boot from floppy (for DOS install disk)
2. Boot the "DOS 6.22" item with floppy
3. Run DOS 6.22 setup, install to C: (P1 will appear as C:)
4. After install, remove One Time Option from boot item

### Phase 4: Install Windows 98 SE (P2)
1. Create boot item "Windows 98 SE"
   - Boot partition: P2
   - Hide: P1, P3, P4, P5
   - Show: P2, P6
   - One Time Option: boot from CD (or Next BIOS Device if CD boots before HDD)
2. Boot the "Windows 98 SE" item with CD
3. Windows 98 setup should see P2 as C: (unformatted or formatted FAT32)
4. Install to C: (P2)
5. After install, remove One Time Option

### Phase 5: Install BeOS R5 (P3)
1. Create boot item "BeOS R5"
   - Boot partition: P3
   - Hide: P1, P2, P4, P5
   - Show: P3 (and P6 optionally)
   - One Time Option: boot from CD
2. Boot the "BeOS R5" item with CD
3. Use BeOS Drive Setup to initialize P3 with BFS (2KB block size recommended)
4. Install BeOS to P3
5. When prompted to install BeOS boot manager (bootman) → **No** (BootIt BM handles booting)
6. After install, remove One Time Option

### Phase 6: Install Windows XP (P4)
1. Create boot item "Windows XP"
   - Boot partition: P4
   - Hide: P1, P2, P3, P5
   - Show: P4, P6
   - One Time Option: boot from CD
2. Boot the "Windows XP" item with CD
3. XP setup should see P4 as unpartitioned space — format as NTFS
4. Install to P4 (will appear as C:)
5. After install, remove One Time Option
6. Note: XP may try to write to the MBR. BootIt BM will survive this (it stores boot code in P0). Reboot into BootIt BM to confirm it still loads.

### Phase 7: Install Linux (P5)
1. Create boot item "Linux"
   - Boot partition: P5
   - Hide: P1, P2, P3, P4
   - Show: P5, P6
   - One Time Option: boot from CD
2. Boot the "Linux" item with CD
3. During install:
   - Partition/format P5 as Ext2/3 (or let installer do it)
   - When asked about boot loader installation → **install to root partition boot sector** (e.g., /dev/hda5), NOT the MBR
   - Do NOT install GRUB/LILO to /dev/hda or /dev/sda
4. Complete install
5. In BootIt BM, verify the "Linux" boot item points to P5 (where GRUB/LILO is installed)
6. BootIt BM will chainload to GRUB/LILO on P5, which then boots Linux

---

## 5. Pitfalls and Gotchas

### DOS 6.22
- **8GB cylinder limit:** DOS 6.22 cannot access anything beyond 8GB on the drive. Keep P1 within the first 8GB.
- **FAT16 only:** DOS cannot see FAT32 partitions. The shared data partition (P6, FAT32) is invisible to DOS.
- **2GB max partition:** FAT16 partition limit is 2GB. P1 at 1GB is safe.
- **Boot files in first clusters:** IO.SYS and MSDOS.SYS must be in the first clusters of the partition. Do not resize P1 from the start after installing DOS — use Slide instead if repositioning is needed.

### Windows 98 SE
- **128GB drive limit:** If the drive exceeds 128GB, Win98 may fail to access the full capacity. Stay under 128GB or use rloew's patched drivers.
- **MBR overwrite:** Win98 setup may overwrite the MBR. BootIt BM recovers from P0, but reboot into BootIt BM immediately after install to verify.
- **Drive letter C:** Win98 expects to be C:. The partition hiding in BootIt BM ensures P2 appears as C: when booted.

### BeOS R5
- **1GB RAM limit:** BeOS crashes with more than 1GB RAM. If the machine has >1GB, limit RAM in BIOS or use a BeOS memory limit flag.
- **Partition type:** BeOS uses BFS (type 0xEB). BootIt BM can create this partition type but you may need to enter 0xEB manually.
- **Block size:** Use 2KB (2048 byte) block size when formatting BFS. 4KB blocks can trigger bugs.
- **Decline bootman:** When BeOS offers to install its own boot manager, say No. BootIt BM is the boot manager.
- **VESA fallback:** BeOS driver support is limited. VESA mode works for most hardware. Expect 16-bit color max on many GPUs.
- **BIOS access:** BeOS R5 uses INT 13h for disk access. Keep P3 within the first 8GB of the drive for reliable booting.

### Windows XP
- **MBR overwrite:** XP setup overwrites the MBR. BootIt BM stores its boot code in P0 and can restore itself. Reboot into BootIt BM after XP install to confirm.
- **NTFS:** XP prefers NTFS. Win98 and DOS cannot read it. The shared data partition (P6) must be FAT32 for cross-OS visibility.
- **Activation:** XP requires activation. If the hardware changes, reactivation may be needed. Consider using a retail license or volume license key.

### Linux
- **Bootloader location:** GRUB/LILO must go in the partition boot sector, NOT the MBR. BootIt BM owns the MBR. If the Linux installer overwrites the MBR, BootIt BM can be restored from the setup media.
- **Disty choice:** For period-correct hardware (PII/PIII), Slackware, Debian 3.x, or Red Hat 7-9 are appropriate. For P4/Athlon XP, slightly newer distros work. Check kernel version for hardware support.
- **Ext2 vs Ext3:** Ext3 (journaling) is safer. Ext2 is simpler and more period-correct for very old hardware.

### General
- **EMBR lock-in:** Once EMBR mode is enabled, no other partitioning tool can be used on this drive. Only BootIt BM.
- **Install order:** Oldest first (DOS), newest last (Linux or XP). This minimizes MBR conflicts.
- **BIOS settings:** Set IDE mode to Legacy/LBA. Disable AHCI if the hardware supports it (retro OSes prefer legacy IDE mode). If using SATA via adapter, ensure BIOS presents drives in legacy mode.
- **Back up before starting:** Create a disk image of the entire drive after each OS install using BootIt BM's built-in Image for DOS. This gives recovery points.

---

## 6. Open Questions

- [ ] **Hardware:** What motherboard/CPU/RAM/drive will be used? This affects partition sizes, BIOS settings, and which Linux distro is appropriate.
- [ ] **Linux distro:** Which distribution? Options: Slackware (period-correct), Debian 3.x, Red Hat 7-9, or a lightweight modern distro if hardware allows.
- [ ] **Drive size:** 40GB? 80GB? 128GB? Affects partition sizing and Win98 compatibility.
- [ ] **DOS data access:** Do you need DOS 6.22 to see shared data? If yes, add a small FAT16 partition below the 8GB line.
- [ ] **BeOS version:** R5 Pro or R5 Personal Edition? R5.1d "Dano" (leaked) is the last version. Haiku is the open-source successor but is not BeOS.
- [ ] **GPU choice:** Affects BeOS driver support and Win98 gaming capability. Voodoo 3 / TNT2 / Rage 128 are period-correct.
- [ ] **BootIt BM license:** Purchase after the 30-day trial confirms the setup works on the target hardware.

---

## 7. Bill of Materials (TBD)

| Component | Specification | Notes |
|---|---|---|
| Motherboard | TBD | Must be BIOS (not UEFI-only) |
| CPU | TBD | PII/PIII ideal for period correctness |
| RAM | TBD | 256MB–512MB sweet spot; BeOS crashes >1GB |
| Storage | TBD | IDE/PATA or IDE-to-SD adapter, 40–128GB |
| GPU | TBD | Period-correct for Win98 gaming |
| Sound card | TBD | Sound Blaster compatible for DOS/Win98 |
| Optical drive | CD/DVD-ROM | For OS install media |
| BootIt BM | License | Trial first, purchase after verification |