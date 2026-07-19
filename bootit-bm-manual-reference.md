# BootIt Bare Metal — Manual Reference for Retro PC Build

> Source: BootIt BM User Manual, revision 2026-05-16 (bootitbm_en_manual.pdf)
> Date: 2026-07-18
> Scope: Settings and procedures relevant to the Pipeline-Retro-PC multi-boot build (Win98 SE + DOS 7.1, BeOS R5 Pro, Server 2003 R2, Debian on a single 128GB IDE drive, EMBR mode).

---

## 1. Settings That Matter for This Build

### Limit Primaries — OFF
The project uses 6 primary partitions (BootIt + 4 OS + 1 data), which requires EMBR mode and Limit Primaries OFF. When OFF, you must use ONLY BootIt BM for partitioning — no FDISK, Windows Disk Management, or DISKPART. This is already stated in the project plan but is worth restating: the manual is emphatic that using other partitioning tools with Limit Primaries disabled risks data corruption.

### Use New Windows MBR — DISABLE for this build
Default is ON (Vista+ compatible MBR code). The manual warns: "The new MBR code will continue to boot older OSes with the exception of some (rare) configurations using Win9x on FAT32."

Since Win98 SE is on a FAT32 partition in this build, disable Use New Windows MBR to avoid the rare-but-possible boot failure. If Win98 won't boot after install, boot the BootIt BM setup media → Partition Work → View MBR → Std MBR to write the old standard MBR code.

### Align on 1MiB Boundaries — OFF
This option is for SSDs (512-byte sector drives aligned on 2048 sectors). For a PATA/IDE drive on a Slot 1 PIII, 1MiB alignment is unnecessary and can cause issues with retro OSes that expect cylinder alignment. Leave it OFF. Default cylinder alignment is correct for this hardware.

### Fix Swap — OFF
Manual: "Enable this option only if your system locks up when you use the Swap option. Warning: Windows 9x may run in compatibility mode if you enable this option." Keep OFF — this is a single-drive build, no swapping needed, and Win98 compatibility mode is a real risk.

### Make HD0 Active — ON (default)
"Required by many new BIOSs when booting from a hard drive other than HD0." Single-drive build, keep default ON.

### Use HD0 in FAT BPB — ON (default)
Forces the FAT BPB drive number to 80h (HD0). Important for Win98 boot reliability — ensures the FAT boot sector points to HD0. Keep default ON.

### MBRs may be Encrypted — ON (default)
Prevents BootIt BM from replacing garbage/encrypted MBR data with a clean empty MBR. Keep default ON to avoid accidental data corruption if any drive has non-standard MBR content.

### BootNow Support — ON (default)
Enables TeraByte's BootNow freeware, which lets you reboot from within Windows (Win32: XP, Server 2003, 7) directly to a selected OS without manually navigating the boot menu. Useful for Server 2003 in this build. Install BootNow separately if you want this feature.

### Virus Check — OFF (default)
Simple MBR virus check on boot. For a retro build handling old floppies/images, consider enabling. Optional.

### IT Mode — OFF (default)
Boots directly to default/last boot item without showing the GUI. Hold Ins or right Ctrl to show the boot menu. Only enable after the build is fully configured and stable, if you want a set-and-forget setup.

---

## 2. Boot Menu: Normal vs Direct

### Normal Boot Menu (use this)
Displays configured boot items. Each item has full MBR Details control — you specify exactly which partitions appear in the MBR for that boot. This is what the project uses: each OS boot item shows its own partition + shared data (P5), hiding all others.

### Direct Boot Menu (not for this build)
Lists all enabled partitions. When not limiting primaries (our case), booting from Direct Boot auto-hides all partitions except the one being booted. Less control than Normal Boot Menu items. The manual notes you can still configure Active/Swap/Hide per partition, but Normal Boot Menu is the right choice for a controlled multi-OS setup.

### Simulated Boot (useful technique)
Hold the LEFT Shift key while clicking Boot (or pressing Enter on a boot item). This updates the MBR partition table per the item's MBR Details WITHOUT actually booting the OS. You hear a beep. Use this to pre-configure the MBR before running an OS installer, or to test that the MBR view is correct.

---

## 3. Boot Item MBR Details (critical for partition hiding)

Each Normal Boot Menu item has an MBR Details section. This is where you control which partitions are visible when that item boots. The manual is explicit: "Failure to correctly load the MBR Details section of the Boot Item is the most common cause of missing partitions."

Operations in MBR Details:
- Fill: load a partition into a blank MBR slot (or replace one). At minimum, the boot partition must be loaded.
- Clear: remove a partition from the MBR (hides it). Only available when Limit Primaries is OFF (our case).
- Hide/Unhide: toggle hidden status. Spacebar also toggles. Hidden partitions stay in the MBR but are marked hidden.
- Volumes / Set Active: hide individual logical volumes within an extended partition. Not relevant — all our partitions are primary.
- Move Up / Move Down: reorder partition table entries. Affects drive letter assignment in some OSes.
- Retain: keep the drive's current MBR contents instead of using the boot item's MBR Details. Special use.
- Ignore: BootIt BM ignores this drive entirely. For encrypted/non-standard drives.

For this build: each boot item uses Fill to add the boot partition + P5 (shared data), and Clear to remove all other OS partitions. This matches the project's boot item table.

### Win9x volume hiding bug
Manual note: "If using DOS through Windows 98, be careful not to hide the last FAT/FAT32 volume as these operating systems have a bug that causes problems mounting partitions if the last volume of an extended partition is not a recognized FAT or FAT32 partition."

Not directly relevant (no extended partitions in this build — all primaries), but worth knowing if the layout ever changes.

---

## 4. One Time Options (for OS installation)

When creating a boot item for installing an OS, set a One Time Option:
- Floppy Drive: boot from floppy. The selected boot partition becomes active.
- Next BIOS Device: boot the next BIOS device (e.g., CD/DVD if BIOS boots HDD first, then CD).
- BIOS Sequence: boot in BIOS-defined order (e.g., if CD boots before HDD).
- Swap: swap the boot drive into HD0 position.

Manual guidance: if the CD/DVD boots after the hard drive in BIOS → use Next BIOS Device. If the CD/DVD boots before the hard drive → use BIOS Sequence.

Also available as hotkeys at the boot menu (no need to pre-configure): Alt+N (Next Device), Alt+S (BIOS Sequence), Alt+F (Floppy). Requires maintenance password if set.

---

## 5. Reactivating After OS Install Overwrites MBR

The manual confirms: "Some operating systems (such as Windows 95/98) deactivate BootIt BM." Server 2003 will also overwrite the MBR.

Recovery procedure:
1. Boot from the BootIt BM setup media (CD/USB/floppy).
2. The Installation & Recovery Boot Menu appears.
3. Select "Reactivate BootIt Bare Metal" → OK.
4. BootIt BM restores itself as the boot manager from its EMBRM partition (P0).

Optional: "Capture MBR" before reactivating if you need to preserve a special MBR. Most users won't need this.

This is the official recovery path — no need to reinstall BootIt BM. Just reactivate after each OS install that overwrites the MBR.

---

## 6. Multi-OS Feature — NOT needed for this build

The manual distinguishes two install modes:
1. **OS in its own single partition** — "the most common choice." One OS per partition, one boot item per OS. This is what the project uses.
2. **Paired partitions / Multi-OS** — Multiple OSes sharing a boot partition, or installing an OS's boot files on one partition and the bulk on another. Requires enabling Multi-OS on the partition and using Groups to manage boot file swapping.

Multi-OS is for running multiple OSes from a single partition (e.g., Win95 and Win98 both booting from C:). This build has one OS per partition, so Multi-OS is unnecessary. Do NOT enable the Multi-OS checkbox on any partition.

### Boot files reference (for Multi-OS, not needed but documented)
- DOS/Win9x (position 0): io.sys, msdos.sys, command.com, autoexec.bat, config.sys
- NT/2000/XP/2003 (position 0): ntldr, boot.ini, ntdetect.com, ntbootdd.sys (SCSI only)

---

## 7. Partition Operations Relevant to This Build

### Create partition
Partition Work → select Free Space → Create. Enter Name, select File System from dropdown (or type decimal/hex if not listed — e.g., BFS 0xEB). Set Size in MB. Optional: Free Space Before/After, Format checkbox (formats immediately if supported), Multi-OS checkbox (do NOT use).

### Format
Partition Work → select partition → Format. For FAT/FAT32/NTFS, can suggest cluster size. BootIt BM determines final valid size. "Align for NTFS conversion" option for FAT/FAT32 — aligns optimally for later NTFS conversion.

### Resize
Partition Work → select partition → Resize. Error-checks first (can take time), then shows Min/Max size. Resize from Start option for supported filesystems. Manual tip: "When resizing old MS/PC DOS FAT partitions from the start is required, Slide should be used instead of Resize since the requirements of the DOS system files (IO.SYS/MSDOS.SYS) being in the first clusters is not adjusted by the resize from start routine." Relevant if ever repositioning the Win98 partition after install — use Slide, not Resize from Start.

### Slide
Partition Work → select partition → Slide. Moves partition within adjacent Free Space. Data Only option for speed. Use this instead of Resize-from-Start for DOS/Win9x partitions to preserve boot file cluster positions.

### Copy
Partition Work → select partition → Copy → select target Free Space → Paste. Data Only option. Delete Source checkbox to move (needed if 4 primaries + Limit Primaries enabled — not our case).

### View MBR
Partition Work → View MBR. Shows MBR partition table. Set Active, Insert, Delete (for EMBR: removes from MBR, not from drive), Move Up/Down, Std MBR (write standard boot code), Win7 MBR, ClearSig (clear NT disk signature), Edit Sig, Fill E-CHS (force ending CHS to max — for LBA mode issues).

### Change Disk Type
Convert between MBR, EMBR, GPT. EMBR required for BootIt BM installation and for >4 primary partitions. Once EMBR, only use BootIt BM for partitioning.

---

## 8. EMBR Mode Notes

- EMBR (Extended Master Boot Record) is TeraByte's spec for >4 primary partitions on an MBR drive.
- Up to 200+ primary partitions. Up to 4 loaded into the MBR partition table per boot item.
- EMBR contains: EMBR Loader, Master Partition Table (MPT, up to 255 entries), boot file table, driver table.
- When an OS boots, the MBR partition table is updated to match the boot item's MBR Details.
- Lock-in: once EMBR is enabled, no other partitioning tool can be used on that drive.

---

## 9. Installation Procedure (from manual, mapped to this build)

### Step A: Create boot media (MakeDisk on a modern Windows machine)
1. Extract bootit_collection_en_v2.zip (full) or bootit_collection_en_trial.zip (trial).
2. Run MakeDisk.exe.
3. Select "PC Platform (BIOS)" → BootIt Bare Metal.
4. Accept license. Trial = leave registration blank (30-day trial).
5. Options:
   - Mouse Support: Enabled (if BIOS supports USB legacy mouse)
   - Video Method: VESA (recommended, falls back to Chipset)
   - Video Mode: 800x600 64K or 1024x768 64K (if supported). Standard VGA 16-color is fallback.
   - Boot Options: Normal (boot to Installation & Recovery menu). Partition Work mode if you only want partitioning.
   - Device Options: Leave defaults (all unselected). Disable SATA Support only if hangs occur.
   - USB Device Options: Leave defaults.
   - Global Geometry: Leave defaults. Do NOT enable Align on 1MiB Boundaries (PATA drive).
   - Additional INI options: TimeZone=EST5EDT (or your zone).
6. Select target: USB/SD (recommended for a PIII without floppy) or ISO (burn to CD).
   - USB Layout: "Partition-MBR FAT/FAT32 Partition" (BIOS detects as hard drive) or "Partition-MBR FAT/FAT32 Partition (Int13h Extensions)" if the PIII BIOS needs it.
   - Default USB layout (Normal-Raw Boot) limits UFD to 1.44MB — avoid.
7. Finish. Media is bootable.

### Step B: Install BootIt BM to hard disk
1. Boot from setup media.
2. Welcome → OK.
3. "Enable support for more than four primary partitions?" → **Yes** (EMBR mode). Optionally select "Change all MBR disk type drives to EMBR" to convert all MBR drives.
4. "How partition chosen?" → Yes (auto) or No (manual). Auto is fine for a blank/single drive.
5. "Install to its own dedicated partition?" → **Yes** (recommended). Creates P0 (~8MB, type 0xDF EMBRM).
6. BootIt BM now owns the MBR.

### Post-install: Configure Settings
Before creating partitions, enter Settings and verify:
- Limit Primaries: OFF (should be, since we enabled >4 primaries)
- Use New Windows MBR: **DISABLE** (Win98 on FAT32)
- Align on 1MiB Boundaries: OFF
- Fix Swap: OFF
- Make HD0 Active: ON
- Use HD0 in FAT BPB: ON
- MBRs may be Encrypted: ON
- BootNow Support: ON (if using BootNow)
- Timeout: set as desired (0 = no auto-boot)

### Create partitions (Partition Work)
Create P1–P5 per the project layout. Do not format P2 (BeOS), P3 (Server 2003), P4 (Debian) — let each OS installer format its own.

### Create boot items (Boot Edit)
For each OS, Add a boot item:
- Identity: name (e.g., "Windows 98 SE")
- HD: 0 (single drive)
- Boot: select the partition
- MBR Details: Fill the boot partition + P5 (shared data). Clear all others.
- One Time Option: Floppy / Next BIOS Device / BIOS Sequence (for install media). Remove after install.
- Default: set one item as default (optional).
- Password: optional per boot item.

### Install each OS
Boot the item with One Time Option set. Install to the partition (appears as C:). After install, boot from setup media → Reactivate BootIt BM. Remove the One Time Option. Repeat for each OS, oldest first.

---

## 10. Keyboard Shortcuts

| Key | Action |
|---|---|
| Esc | Cancel |
| F1 | Help |
| F10 | OK / Apply / Close |
| F11 | Save boot menu position |
| Shift+F11 | Reset boot menu to center |
| F12 | Capture screen to floppy A: |
| Ins | Add / Create / Fill |
| Del | Delete / Clear |
| Tab / Shift+Tab | Next / Previous control |
| Arrow keys | Move / increment |
| Spacebar | Toggle checkbox / hide-unhide / set active |
| Alt+M | Maintenance Mode (from boot menu) |
| Alt+0 | Power off (at boot menu/desktop) |
| Ctrl+Alt+Del | Reboot |
| Alt+N | One-time Next BIOS Device |
| Alt+S | One-time BIOS Sequence |
| Alt+F | One-time Floppy boot |
| Left Shift + Boot | Simulated boot (update MBR only) |
| Alt+underlined letter | Direct control access |

---

## 11. UI Layout (from manual screenshots)

### Main Desktop
The BootIt BM desktop has icon buttons for the primary functions:
- **Partition Work** — opens the Work with Partitions dialog
- **Boot Edit** — opens the Boot Menu editor (Normal Boot Menu items)
- **Settings** — opens the Program Settings dialog
- **Disk Imaging** — launches Image for DOS (if installed)
- **Text Editor** (press X) — edit files, create batch/script files
- **Run** — run .BAT/.CMD/.RUN/.TBS scripts
- **Resume** — exit maintenance mode and boot the selected item

### Boot Menu Item Dialog (Boot Edit → Add/Edit)
Three main sections:

**Boot Details:**
- Identity — name displayed on boot menu (type & before a letter for shortcut key)
- Memo — descriptive text shown on boot menu
- HD — hard drive number (0-based) containing the boot partition
- OSI — OS Loader Item Index (Vista+ only, one-based; 0 = disabled)
- Icon — icon for boot menu entry
- Boot — partition/volume to boot (dropdown)
- Group — Multi-OS group name (only for Multi-OS partitions — not this build)
- Captured — captured MBR/LVM file (rarely needed)
- Script — .tbs/.run script to run before boot
- Sound — .snd sound file to play on boot
- Floppy Drive — boot from floppy (boot partition becomes active)
- Next BIOS Device — boot next BIOS device (e.g., CD if HDD is first)
- Swap — swap HD into HD0 position
- BIOS Sequence — boot in BIOS-defined order
- Default — mark as default boot item
- Ignore A20 — for older OSes that won't boot properly

**MBR Details** (partition visibility control):
The partition list shows entries in the MBR partition table for each drive. Buttons:
- **Fill** — load a partition into a blank/selected MBR slot
- **Clear** — remove partition from MBR (hides it). Only when Limit Primaries is OFF.
- **Hide / Unhide** — toggle hidden status (or press Spacebar)
- **Volumes / Set Active** — hide logical volumes (MBR) or set active (GPT)
- **Move Up / Move Down** — reorder partition table entries (affects drive letters)
- **Retain** — keep drive's current MBR contents instead of boot item config
- **Ignore** — BootIt BM ignores this drive entirely

This is the critical section for the multi-OS build: for each boot item, Fill the boot partition + P5 (shared data), Clear all other OS partitions. Hidden status can also be toggled with the Spacebar.

**One Time Option:**
- Floppy Drive, Next BIOS Device, Swap, BIOS Sequence (same as Boot Details versions, but one-time)
- Keystrokes: Input (up to 15 keystrokes replayed on boot) + Clear
- Password: Keyword to require on boot (note: password-protected items can't use BootNow)

### Work with Partitions Dialog (Partition Work)
- **Drives dropdown** — select drive (generic names or model numbers)
- **Bus dropdown** — access method: BIOS, BIOS (direct), USB, IEEE1394
- **Disk Type** displayed: MBR, EMBR, or GPT
- **Partition list** — shows partitions and Free Space; C/H/S values in lower-left
- **Errors indicator** — "* Errors Exist *" appears if partition structure problems detected; affected partitions marked with E
- **Actions:**
  - Create — create partition/volume from Free Space (Name, File System, Size, Free Space Before/After, Format, Multi-OS checkboxes)
  - Delete — delete partition (optional: Clear Boot Sector, Wipe Partition, Secure Wipe, Trim)
  - Undelete — recover deleted FAT/HPFS/NTFS/Ext2fs/ReiserFS/Extended partitions
  - Format — format partition (cluster size, Align for NTFS conversion for FAT/FAT32)
  - Resize — error-check then resize (Min/Max size, Resize from Start option)
  - Copy — copy partition to Free Space (Data Only, Delete Source options)
  - Slide — move partition within adjacent Free Space (Data Only option)
  - Align — align FAT/FAT32 for optimal NTFS conversion
  - Properties — view/edit partition properties (Name, Hide/Unhide, Multi-OS, Disable Fast Start for Win8+)
  - BCD Edit — edit Windows BCD store (Vista/7)
  - Edit File — edit text files up to 64K on supported filesystems

### Create Partition Dialog
- **Partition Information:** Name, File System (dropdown or type decimal/hex), Size (MB)
- **Free Space Before / Free Space After** — position the partition within Free Space
- **Options:** Format checkbox (formats immediately if FS supported), Multi-OS checkbox (do NOT use for this build)
- For FAT/FAT32/NTFS with Format selected: cluster size suggestion + Align for NTFS conversion option

### Format Dialog
- **Partition Information** — shows Name, Type, Size
- **Cluster size** suggestion (FAT/FAT32/NTFS) — BootIt BM determines final valid size
- **Align for NTFS conversion** (FAT/FAT32 only) — aligns for optimal later NTFS conversion
- OK → formats, scans for errors (FAT/FAT32, can cancel), shows Format Summary

### Settings Dialog
Tabs/sections: Startup, General, Device, Global Geometry and Alignment, Security, Time Zone, Users

**Startup:** Timeout (0 = no auto-boot), Sound, Background (PCX only), Direct Boot Menu checkbox, Display DB Button

**General (key options for this build):**
- Limit Primaries (OFF for this build — enables EMBR)
- Use Volume Label (ON default)
- Fix Swap (OFF — Win9x compatibility mode risk)
- Use Boot Item Disk IDs (OFF default)
- ISO8601 Date/Time (OFF default)
- Completion Alarm (OFF default)
- BootNow Support (ON default — useful for Server 2003)
- IT Mode (OFF default)
- MBR Boot Ctrl on GPT (OFF default)
- Full Partition List (OFF default)
- Virus Check (OFF default)
- MBRs may be Encrypted (ON default)
- Keep HD Active (ON default)
- **Use New Windows MBR (ON default — DISABLE for this build due to Win98 on FAT32)**
- Volume Sequence as ID (OFF default)
- Assume Original HD (OFF default)
- Use HD0 in FAT BPB (ON default — keep)
- Make HD0 Active (ON default — keep)
- Resize with Caching (ON default)
- Disable Fixups (OFF default)

**Device:** PATA Support (ON), PATA IRQC (OFF), SATA-AHCI (ON), Disable SATA Bias, IEEE1394 Support (ON), USB 2.0 Support (ON), Enable USB 1.1 UHCI (OFF), USB Controller Hang Fix, USB Device Hang Fix

**Global Geometry and Alignment:** Disable (OFF), Align on 1MiB Boundaries (OFF for this build), Align on Cylinder, Assume Same Target System, Validate Geometry Before Use, Align MBR for BIOS Auto Mode (ON default), Use Source Host Geometry

**Security:** Maintenance Password, Require Login, Users

### Installation & Recovery Boot Menu
Appears when booting from BootIt BM setup media after BootIt BM is installed. Options:
- **Reactivate BootIt Bare Metal** — restore BootIt BM as boot manager (use after OS install overwrites MBR)
- Capture MBR — save current MBR for special OSes (rarely needed)
- Capture LVM Data — for OS/2 (not relevant)
- Access BootIt Bare Metal Partition — mount the EMBRM partition (P0) to access files
- Upgrade BootIt Bare Metal — upgrade existing installation

---

## 11. Pitfalls Specific to This Build (from manual)

1. **Win98 on FAT32 + new MBR code**: May fail to boot. Disable Use New Windows MBR, or write Std MBR via View MBR.
2. **Resize from Start on Win98 partition**: Breaks IO.SYS/MSDOS.SYS first-cluster requirement. Use Slide instead.
3. **Other partitioning tools with EMBR**: Data corruption risk. Only use BootIt BM.
4. **OS install overwrites MBR**: Expected. Use Reactivate BootIt BM from setup media. Not a failure.
5. **Win9x deactivates BootIt BM**: Manual confirms Win95/98 deactivate it. Reactivate after install.
6. **Fix Swap + Win9x**: Causes Win9x compatibility mode. Keep Fix Swap OFF.
7. **1MiB alignment on PATA**: Not for this hardware. Cylinder alignment is correct.
8. **Direct Boot Menu auto-hide**: If accidentally using Direct Boot, only the booted partition is visible (when not limiting primaries). Use Normal Boot Menu for controlled visibility.
9. **UFD >64GB**: MakeDisk won't list USB drives >64GB by default. Click USB+ button or run `makedisk /nousblimit`. Use a smaller USB drive if possible.
10. **Boot media settings**: Only video mode/method and mouse support come from the setup media at boot. All other settings come from the installed bootitbm.ini. Configure settings in BootIt BM itself, not just on the media.