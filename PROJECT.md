# PROJECT.md — Pipeline-Retro-PC

**Created:** 2026-07-09
**Status:** Planning — hardware confirmed, partition layout and install sequence defined
**Goal:** Multi-boot DOS 6.22, Windows 98 SE, BeOS R5 Pro (with BONE network stack), Windows Server 2003 (converted to gaming workstation), and Debian Linux from a single 128GB IDE drive using BootIt Bare Metal

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

### BeOS R5 Pro (with BONE network stack)
- Filesystem: BFS (Be File System) — native, journaling, 64-bit capable
- Min requirements: 32MB RAM, 200MB disk space, Pentium-class CPU
- Max RAM: ~1GB (crashes above this)
- Partition type: 0xEB (BeOS BFS) — BootIt BM supports custom partition types
- Can install its own boot manager (bootman) — but we will use BootIt BM instead, so decline bootman during install
- BeOS Drive Setup can partition and format BFS; alternatively pre-create the partition in BootIt BM and format with 2KB block size
- Recommended partition size: 2GB (BeOS R5 is small, but BONE + apps need room)
- Position constraint: ideally within first 8GB for safest BIOS access (BeOS R5 uses INT 13h)
- **BONE install:** Install BeOS R5 Pro first, then install the BONE (BeOS Networking Environment) package on top of R5 Pro. BONE is the improved network stack from the Dano codebase, but can be installed without upgrading the entire OS to Dano 5.1d. The original R5 net_server stack is poor (missing getsockopt, etc.). BONE replaces it with the newer Dano-era networking stack while keeping R5 Pro as the base OS.

### Windows Server 2003 R2 (converted to gaming workstation)
- Filesystem: NTFS (recommended) or FAT32
- Can be installed on a primary partition
- Uses NTLDR/BOOT.INI boot mechanism (same as XP — NT 5.2 kernel)
- No cylinder/size limit with SP2+
- Can read FAT16, FAT32, NTFS
- **Edition confirmed:** 2-disc R2 release. Disc 1 = Server 2003 SP1 (this is the base OS to install and convert). Disc 2 = R2 features (ADFS, MMC 3.0, DFS, Virtual Server 2005, Services for UNIX). Disc 2 is server-oriented — skip it for the workstation build, or install only if you want updated MMC and .NET Framework 2.0.
- **Why Server 2003 instead of XP:** NT 5.2 kernel, leaner than XP, runs better on 500MHz/256MB. Stripped of consumer bloat (Luna, System Restore, Fast User Switching). More stable. See server2003-workstation-conversion.md for full conversion guide.
- **Conversion required:** Post-install or via nLite pre-install. Enable audio, DirectX, hardware acceleration. Disable IE Enhanced Security, shutdown event tracker, server services. See server2003-workstation-conversion.md.
- **Recommendation:** Use Standard Edition (32-bit, IA-32). Supports 4 CPUs and 4GB RAM. Most commonly available, best documented for workstation conversion.

### Linux (Debian)
- Filesystem: Ext2/3/4, ReiserFS, etc.
- BootIt BM cannot boot Linux directly — chainloads to LILO or GRUB installed in the Linux partition's boot sector (NOT the MBR)
- Can be on a primary or logical partition
- During install: answer "No" to "install bootloader to MBR" and specify the root partition's boot sector
- **Distribution: Debian** — period-appropriate options:
  - Debian 3.1 Sarge (2005) — i386, period-correct for PIII, supports i686
  - Debian 4.0 Etch (2007) — i386, also supports PIII
  - Debian 8 Jessie (2015) — last Debian to support pre-i686 CPUs (not relevant for PIII, but reference)
  - Debian 9 Stretch (2016+) — dropped i486/i586, but PIII is i686 so still supported
  - Debian 12 Bookworm (2023) — confirmed running on 933MHz PIII by Reddit users; i686 port works on PIII
  - **Recommendation:** Debian 3.1 Sarge for period correctness, or Debian 12 if you want a usable modern system. PIII is i686, so current Debian i386 port works. The tradeoff is period-accuracy vs. package availability.
- Debian ISO archive: https://cdimage.debian.org/cdimage/archive/

---

## 2. Hardware (Confirmed)

| Component | Specification | Notes |
|---|---|---|
| CPU | Intel Pentium III (Slot 1) | i686 architecture — supports all current Debian i386 builds |
| Motherboard | Slot 1 chipset (likely i440BX or i815) | Must verify 48-bit LBA BIOS support for 128GB drive (see below) |
| RAM | TBD — target 256MB–512MB | BeOS crashes above 1GB. DOS/Win98 do not benefit from >512MB. |
| Storage | 128GB IDE/PATA hard drive | Within Win98 128GB limit. Verify BIOS supports 48-bit LBA. |
| GPU | TBD | Period-correct for Win98 gaming (Voodoo 3, TNT2, etc.). BeOS uses VESA fallback. |
| Sound card | TBD | Sound Blaster compatible for DOS/Win98 |
| Optical drive | CD/DVD-ROM | For OS install media |
| BootIt BM | License | Trial first, purchase after verification |

### Critical: 128GB Drive and 48-bit LBA

The 128GB IDE drive is exactly at the 28-bit LBA limit (137.4GB). A Slot 1 motherboard (i440BX era, 1998-2000) may or may not support 48-bit LBA in BIOS. This determines whether the BIOS can see the full 128GB.

**What to check:**
- Enter BIOS setup and look for the drive auto-detect. If it reports ~128GB (or ~120GB binary), 48-bit LBA is supported.
- If it reports ~137GB or less, or wraps around, the BIOS lacks 48-bit LBA.
- i440BX boards with BIOS updates from 2002+ often gained 48-bit LBA support.
- If the BIOS does NOT support 48-bit LBA: use a BIOS overlay tool (like Ontrack Disk Manager or EZ-Drive) OR install a Promise/Maxtor PCI IDE controller with its own BIOS that supports 48-bit LBA.
- BootIt BM itself can access drives via direct I/O (bypassing BIOS), but the BIOS must still see the drive at boot.

**Win98 SE note:** Win98 SE does NOT natively support 48-bit LBA. Even with a BIOS that supports it, Win98's IDE driver is limited to 128GB. This is a hard limit — the 128GB drive is exactly at this boundary. It will work, but do not exceed 128GB. rloew's 48-bit LBA patch exists if you ever want to go larger.

**BeOS note:** BeOS R5 uses INT 13h for disk access. It should handle the 128GB drive if the BIOS supports 48-bit LBA. Keep the BeOS partition within the first 8GB for safety.

---

## 3. Partition Layout (Single Drive)

Requires EMBR mode in BootIt BM (more than 4 primary partitions needed: BootIt partition + 5 OS partitions + 1 data partition = 7 total).

Drive size assumption: 40GB–128GB (TBD based on hardware). Layout shown with example 80GB drive.

| Partition | Type | FS | Size | Purpose | Position Constraint |
|:---:|:---:|:---:|:---:|---|---|
| P0 | EMBRM (0xDF) | — | ~8MB | BootIt BM boot manager | Must be first |
| P1 | Primary | FAT16 | 1GB | DOS 6.22 | Must be within first 8GB |
| P2 | Primary | FAT32 | 10GB | Windows 98 SE | Within 128GB limit |
| P3 | Primary | BFS (0xEB) | 2GB | BeOS R5 Pro + BONE | Within first 8GB for safe BIOS access |
| P4 | Primary | NTFS | 20GB | Win Server 2003 R2 (workstation) | No constraint |
| P5 | Primary | Ext3 | 10GB | Debian Linux | GRUB/LILO in boot sector, not MBR |
| P6 | Primary | FAT32 | ~85GB | Shared data | Visible from Win98, XP, Debian |

### Boot Items in BootIt BM

Each boot item shows only the target OS partition and the shared data partition. All other OS partitions are hidden.

| Boot Menu Item | Boot Partition | Visible Partitions | Hidden Partitions |
|---|:---:|---|---|
| DOS 6.22 | P1 (FAT16) | P1 | P2, P3, P4, P5, P6 |
| Windows 98 SE | P2 (FAT32) | P2, P6 | P1, P3, P4, P5 |
| BeOS R5 Pro (BONE) | P3 (BFS) | P3, P6* | P1, P2, P4, P5 |
| Win Server 2003 R2 | P4 (NTFS) | P4, P6 | P1, P2, P3, P5 |
| Debian Linux | P5 (Ext3) | P5, P6** | P1, P2, P3, P4 |

DOS does not see P6 (FAT32 is invisible to DOS 6.22). User confirmed DOS does not need shared data access.

*BeOS can read FAT32 via BFS add-ons, but BFS is native. P6 visibility optional.

**Debian can read FAT32, NTFS, and Ext3. P6 as FAT32 is readable from Debian.

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

### Phase 5: Install BeOS R5 Pro + BONE (P3)
1. Create boot item "BeOS R5 Pro"
   - Boot partition: P3
   - Hide: P1, P2, P4, P5
   - Show: P3 (and P6 optionally)
   - One Time Option: boot from CD
2. Boot the "BeOS R5 Pro" item with R5 Pro CD
3. Use BeOS Drive Setup to initialize P3 with BFS (2KB block size recommended)
4. Install BeOS R5 Pro to P3
5. When prompted to install BeOS boot manager (bootman) -> No (BootIt BM handles booting)
6. After R5 Pro is installed, install the BONE network stack:
   - Boot into R5 Pro
   - Install the BONE (BeOS Networking Environment) package on top of R5 Pro
   - BONE replaces the original net_server stack with the improved Dano-era networking stack
   - Reboot and verify BONE is active (Network preferences should reflect the new stack)
7. After BONE install, remove One Time Option from boot item

### Phase 6: Install Windows Server 2003 R2 (P4)
1. Create boot item "Win Server 2003 R2"
   - Boot partition: P4
   - Hide: P1, P2, P3, P5
   - Show: P4, P6
   - One Time Option: boot from CD
2. Boot the "Win Server 2003 R2" item with Disc 1 (Server 2003 SP1)
3. Server 2003 setup should see P4 as unpartitioned space — format as NTFS
4. Install to P4 (will appear as C:)
5. Disc 2 (R2 features) is optional — skip it for workstation use, or install only if you want updated MMC and .NET Framework 2.0
6. After install, perform the workstation conversion (see server2003-workstation-conversion.md):
   - Disable IE Enhanced Security Configuration
   - Disable Shutdown Event Tracker
   - Enable Windows Audio service
   - Enable DirectX and hardware acceleration (full)
   - Set performance to Programs (not background services)
   - Disable unnecessary server services (see conversion guide)
   - Install DirectX 9.0c
   - Install GPU/sound/chipset drivers (XP drivers work)
7. After conversion, remove One Time Option
8. Note: Server 2003 R2 may try to write to the MBR. BootIt BM will survive this (it stores boot code in P0). Reboot into BootIt BM to confirm it still loads.

### Phase 7: Install Debian Linux (P5)
1. Create boot item "Debian Linux"
   - Boot partition: P5
   - Hide: P1, P2, P3, P4
   - Show: P5, P6
   - One Time Option: boot from CD
2. Boot the "Debian Linux" item with Debian install CD
3. During install:
   - Partition/format P5 as Ext3 (or let installer do it)
   - When asked about boot loader installation -> install to root partition boot sector (e.g., /dev/hda5 or /dev/sda5), NOT the MBR
   - Do NOT install GRUB to /dev/hda or /dev/sda (MBR) — BootIt BM owns the MBR
   - If using LILO (Debian 3.1 Sarge), same rule: install to root partition, not MBR
4. Complete install
5. In BootIt BM, verify the "Debian Linux" boot item points to P5 (where GRUB/LILO is installed)
6. BootIt BM will chainload to GRUB/LILO on P5, which then boots Debian
7. After install, remove One Time Option

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

### BeOS R5 Pro / BONE
- **1GB RAM limit:** BeOS crashes with more than 1GB RAM. If the machine has >1GB, limit RAM in BIOS or use a BeOS memory limit flag.
- **Partition type:** BeOS uses BFS (type 0xEB). BootIt BM can create this partition type but you may need to enter 0xEB manually.
- **Block size:** Use 2KB (2048 byte) block size when formatting BFS. 4KB blocks can trigger bugs.
- **Decline bootman:** When BeOS offers to install its own boot manager, say No. BootIt BM is the boot manager.
- **VESA fallback:** BeOS driver support is limited. VESA mode works for most hardware. Expect 16-bit color max on many GPUs.
- **BIOS access:** BeOS R5 uses INT 13h for disk access. Keep P3 within the first 8GB of the drive for reliable booting.
- **BONE install:** Install BONE on top of R5 Pro (not a full Dano upgrade). BONE replaces the original net_server stack with the improved Dano-era networking stack. The original R5 net_server stack lacks getsockopt and other modern socket calls. Verify BONE is active after install via Network preferences.
- **BONE compatibility:** Some R5 Pro apps may not work with BONE. Test critical apps after install. BONE changes the networking layer, which can affect older network utilities.

### Windows Server 2003 R2 (workstation)
- **MBR overwrite:** Server 2003 R2 setup overwrites the MBR. BootIt BM stores its boot code in P0 and can restore itself. Reboot into BootIt BM after install to confirm.
- **NTFS:** Server 2003 R2 prefers NTFS. Win98 and DOS cannot read it. The shared data partition (P6) must be FAT32 for cross-OS visibility.
- **Activation:** Server 2003 R2 requires activation. Retail or volume license keys work.
- **R2 disc 2:** Optional for workstation use. Skip it unless you need .NET Framework 2.0 or updated MMC.
- **Game installers:** Some games refuse to install on Server 2003 (OS version check). Use compatibility mode (Windows XP) or edit the MSI LaunchCondition table. See server2003-workstation-conversion.md Step 11.
- **16-bit apps:** NTVDM support is present but may need AUTOEXEC.NT replacement. See conversion guide Step 12.
- **NVIDIA drivers:** May not auto-detect Server 2003. Install XP drivers, then manually add the card through Device Manager > Update Driver > Have Disk.
- **Performance:** After conversion, Server 2003 R2 will run lighter than XP on 500MHz/256MB. Keep Classic theme (not Luna) for maximum performance.

### Debian Linux
- **Bootloader location:** GRUB/LILO must go in the partition boot sector, NOT the MBR. BootIt BM owns the MBR. If the Debian installer overwrites the MBR, BootIt BM can be restored from the setup media.
- **Debian version choice:** PIII is i686, so both period-correct and modern Debian i386 builds work:
  - Debian 3.1 Sarge (2005): Period-correct, uses LILO by default, lighter package set
  - Debian 12 Bookworm (2023): Modern packages, confirmed working on 933MHz PIII, uses GRUB2
  - Tradeoff: period accuracy (Sarge) vs. package availability and security updates (Bookworm)
- **Ext3 vs Ext2:** Ext3 (journaling) is safer against power loss. Ext2 is simpler and more period-correct for Sarge era.
- **48-bit LBA in Linux:** Linux kernel 2.4+ supports 48-bit LBA natively. The 128GB drive will be fully visible from Debian regardless of which version is chosen.

### General
- **EMBR lock-in:** Once EMBR mode is enabled, no other partitioning tool can be used on this drive. Only BootIt BM.
- **Install order:** Oldest first (DOS), newest last (Linux or XP). This minimizes MBR conflicts.
- **BIOS settings:** Set IDE mode to Legacy/LBA. Disable AHCI if the hardware supports it (retro OSes prefer legacy IDE mode). If using SATA via adapter, ensure BIOS presents drives in legacy mode.
- **Back up before starting:** Create a disk image of the entire drive after each OS install using BootIt BM's built-in Image for DOS. This gives recovery points.

---

## 6. Open Questions

- [ ] **Motherboard model:** Which Slot 1 board? Need to verify 48-bit LBA BIOS support for the 128GB drive. Common i440BX boards (Asus P2B, Abit BE6) may need a BIOS update.
- [ ] **RAM amount:** How much RAM is installed? BeOS crashes above 1GB.
- [ ] **Debian version:** Sarge (period-correct) or Bookworm (modern, confirmed on PIII)?
- [ ] **GPU:** What graphics card? Affects BeOS driver support and Win98 gaming.
- [ ] **Sound card:** What sound card? DOS/Win98 need Sound Blaster compatibility.
- [ ] **BeOS Dano source:** Confirm you have the Dano 5.1d upgrade files.
- [ ] **BootIt BM license:** Purchase after the 30-day trial confirms the setup works on the target hardware.

---

## 7. Bill of Materials

| Component | Specification | Status | Notes |
|---|---|---|---|
| CPU | Intel Pentium III (Slot 1) | Confirmed | i686 architecture |
| Motherboard | Slot 1 chipset (i440BX or i815) | TBD — need model | Verify 48-bit LBA BIOS support |
| RAM | 256MB–512MB target | TBD | BeOS crashes >1GB |
| Storage | 128GB IDE/PATA hard drive | Confirmed | At Win98 128GB limit boundary |
| GPU | Period-correct (TBD) | TBD | Voodoo 3 / TNT2 / Rage 128 for Win98 + BeOS VESA |
| Sound card | Sound Blaster compatible (TBD) | TBD | For DOS/Win98 audio |
| Optical drive | CD/DVD-ROM | TBD | For OS install media |
| BootIt BM | License (trial first) | Needed | 30-day trial, then purchase |
| DOS 6.22 | Install media | Needed | Floppy disks or CD |
| Windows 98 SE | Install media | Needed | Boot floppy + CD |
| BeOS R5 Pro | Install media | Needed | CD |
| BeOS BONE | Network stack package | Needed | Installed on top of R5 Pro |
| Win Server 2003 R2 | 2-disc set + license | Confirmed | Disc 1 = SP1 base install, Disc 2 = R2 features (optional). Convert to workstation post-install. |
| Debian Linux | Install CD | Needed | Sarge 3.1 or Bookworm 12 |