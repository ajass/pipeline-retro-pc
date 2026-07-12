# nLite Configuration Guide for Server 2003 R2 Gaming Workstation

> Target: Windows Server 2003 R2 Standard (32-bit), Disc 1 (SP1 base)
> Hardware: Slot 1 Pentium III, 500MHz, 256MB RAM, 128GB IDE
> nLite version: 1.4.9.3
> Goal: Lean gaming/workstation OS — strip server bloat, enable multimedia, maximize free RAM

---

## Overview

nLite has four main task areas. You select which to enable at the start:

1. **Integrate** — service packs, hotfixes, drivers, addons
2. **Remove** — components you do not need
3. **Setup** — unattended install configuration
4. **Tweaks** — registry tweaks, services configuration, patches

Run nLite on a modern Windows machine (needs .NET Framework 2.0+). Feed it the Disc 1 I386 folder. The output is a bootable ISO.

---

## Part 1: What to Integrate

### Service Pack

Integrate **Server 2003 SP2** onto the SP1 base. SP2 supersedes SP1 — no need to slipstream SP1 first.

- Download: Windows Server 2003 Service Pack 2 (32-bit x86) from Microsoft Update Catalog or archive sites
- In nLite: select "Service Pack" task, point to the SP2 installer
- nLite slipstreams it into the I386 source

### Hotfixes

Optional. If you want post-SP2 security updates:

- Download individual hotfixes from Microsoft Update Catalog (requires Internet Explorer or archive sites)
- nLite can integrate them via "Hotfixes" task
- For a retro gaming machine that will not be on the internet 24/7, this is low priority. The May 2019 final security rollup is the last one worth integrating.
- Alternatively, use the Legacy Update tool after install to pull updates from the catalog

### Drivers

Integrate drivers that the Server 2003 installer will need at boot time:

- **Mass storage / IDE controller drivers** — only if the motherboard uses a non-standard IDE controller. For i440BX/i815 with standard PATA, the default drivers work. Skip unless you have a PCI IDE card.
- **GPU drivers** — do NOT integrate these. Install GPU drivers after the OS is running. nLite-integrated GPU drivers can cause setup problems.
- **Audio drivers** — do NOT integrate. Install after OS.
- **Chipset drivers** — optional. XP/2003 chipset INF drivers can be integrated if you have them, but for i440BX the default Microsoft drivers work fine.

**Recommendation:** Skip driver integration unless you have a specific mass storage driver need. Install all drivers post-OS.

### Addons

Optional community packages you can integrate:

- **DirectX 9.0c redistributable** — integrate as an addon so it is present at first boot. Saves a step later.
- **.NET Framework 2.0** — if you skipped R2 Disc 2 but want .NET support. Integrate as addon.
- **Legacy Update** — a tool that fixes Windows Update on legacy OSes. Integrate as addon or install later.

---

## Part 2: What to Remove

nLite marks components as black (safe to remove) or red (critical, removing may break things). Below is a curated list for a gaming workstation on a PIII with 256MB RAM.

### Applications — REMOVE

| Component | Action | Reason |
|---|---|---|
| Accessibility Options | Remove | Vision/hearing/mobility enhancements. Not needed for gaming. |
| Briefcase | Remove | File sync between desktop/laptop. Not needed. |
| Internet Games | Remove | XP-era multiplayer internet games. Useless. |
| NT Backup | Remove | Server backup tool. Use BootIt BM imaging instead. |
| Pinball | Remove | Built-in game. Not needed. |
| Screensavers | Remove | Default screensavers. Does not remove screensaver support itself. |

### Applications — KEEP

| Component | Reason |
|---|---|
| Calculator | No reason to remove. |
| Charmap | Useful for special characters. |
| Defragmenter | Keep unless using third-party defrag. |
| Games | Solitaire, Minesweeper — negligible size, keep if you want them. |
| Paint | Useful for screenshots. |
| Wordpad | Useful for reading RTF/text files without Office. |

### Drivers — REMOVE

| Component | Action | Reason |
|---|---|---|
| ATM (Asynchronous Transfer Mode) | Remove | No ATM hardware on a retro PC. |
| Cameras and Camcorders | Remove | Install drivers manually if needed later. |
| Display Drivers (old) | Remove | S3, Cirrus Logic, Diamond — not using these on a PIII gaming rig. |
| IBM ThinkPad | Remove | Not a ThinkPad. |
| ISDN | Remove | No ISDN hardware. |
| Modems | Remove | No dial-up modems. |
| Multifunctional | Remove | No combo cards. |
| Portable Audio | Remove | No portable audio players. |
| Printers | Remove | Install printer drivers manually if needed. |
| Scanners | Remove | Install scanner drivers manually if needed. |
| SCSI/RAID | Remove | No SCSI/RAID controllers. (Keep if using CD-ROM emulation like Daemon Tools.) |
| Serial Pen Tablet | Remove | No tablet hardware. |
| Sony Jog Dial | Remove | Sony-specific, not needed. |
| Sound Controllers | Remove | You will install Sound Blaster drivers manually. |
| Tape Drives | Remove | No tape drives. |
| Toshiba DVD decoder card | Remove | No Toshiba decoder card. |
| Wireless Ethernet (WLAN) | Remove | No wireless on a Slot 1 PIII. Install manually if you add a WiFi card later. |

### Drivers — KEEP

| Component | Reason |
|---|---|
| Display Drivers (default) | Keep as fallback. You will install GPU drivers manually, but the default VGA driver is needed for setup. |
| Ethernet (LAN) | Keep. Needed for networking. Common Realtek/SiS chipsets included. |
| Floppy Support | Keep if you have a floppy drive. Needed for DOS/Win98 boot disks. |
| Firewire (1394) | Keep if you have Firewire ports. Some retro hardware uses it. |
| Joystick Support | Keep if using gameport joysticks. |
| Logical Disk Manager | Keep. Needed for drive letter assignment and disk management. |
| Multi Processor Support | Keep. Some Slot 1 boards are dual-socket. Does not hurt to keep. |
| Ports (COM & LPT) | Keep. Needed for serial mice and parallel port devices. |
| USB Audio support | Keep if using USB audio devices. |
| USB Ethernet | Keep if using USB Ethernet adapters. |

### Hardware — REMOVE

| Component | Action | Reason |
|---|---|---|
| AGP Filters | Remove | Not needed on i440BX with standard AGP. |
| ALI 1535 SMBus | Remove | No ALI chipset. |
| ALI IDE Controller | Remove | No ALI chipset. |
| ATM Support | Remove | No ATM network. |
| Battery | Remove | Desktop, not laptop. |
| Bluetooth Support | Remove | No Bluetooth on retro PIII. |
| Brother Devices | Remove | No Brother hardware. |
| CMD PCI IDE Controller | Remove | No CMD controller. |
| CPU AMD | Remove | Intel PIII, not AMD. |
| CPU Transmeta Crusoe | Remove | Not Transmeta. |
| Gravis Digital Game Port | Remove | No Gravis gameport. (Keep if using a Gravis gamepad.) |
| IEEE 1284.4 Devices (Dot4) | Remove | No Dot4 devices. |
| Infrared | Remove | No IR hardware. |
| Iomega Zip Drive | Remove | No Zip drive. |
| Microsoft Color Management (ICM) | Remove | Color management not critical for gaming. |
| Modem Support | Remove | No modems. |
| Multi-port serial adapters | Remove | No multi-port serial. |
| PCMCIA | Remove | Desktop, no PCMCIA. |
| Ramdisk | Remove | Not needed. |
| Secure Digital Host Controller | Remove | No SD card slot. |
| Smartcards | Remove | No smartcard reader. |
| Sony Memory Stick | Remove | No Memory Stick. |
| Teletext Codec | Remove | No teletext. |
| Toshiba PCI IDE Controller | Remove | No Toshiba controller. |
| USB Video Capture | Remove | No USB video capture. |
| Windows CE USB Host | Remove | No WinCE devices. |

### Hardware — KEEP

| Component | Reason |
|---|---|
| CPU Intel | Required for Intel PIII. |
| Firewire Network Support | Keep if using Firewire networking. |
| Intel PCI IDE Controller | Required for i440BX/i815 IDE. |
| Printer Support | Keep if you ever want to install a printer. |
| USB Audio support | Keep if using USB audio. |
| Windows Image Acquisition (WIA) | Keep if using scanners/cameras. Otherwise remove. |

### Multimedia — REMOVE

| Component | Action | Reason |
|---|---|---|
| AOL ART Image Format Support | Remove | Useless. |
| Images and Backgrounds | Remove | Default wallpapers. Negligible savings but not needed. |
| Intel Indeo Codecs | Remove | Old video codec. Install if a specific game needs it. |
| Luna Theme | Remove | XP theme engine. Not present on Server 2003 by default, but if available, remove it. Classic theme only for performance. |
| Media Center | Remove | Not a Media Center distribution. |
| Mouse Cursors | Remove | Extra cursor themes. Keep default. |
| Movie Maker | Remove | Not needed for gaming. |
| Music Samples | Remove | Useless sample music. |
| Old CD Player and Sound Recorder | Remove | Classic utilities, not needed. |
| Speech Support | Remove | Not needed for gaming. |
| Tablet PC | Remove | No tablet. |
| Windows Media Player 6.4 | Remove | Keep only if a game specifically requires it. |

### Multimedia — KEEP

| Component | Reason |
|---|---|
| ACM Core Codecs | Required for audio. |
| Active X for Streaming Video | Keep if using streaming video in IE. |
| DirectX | Keep. Absolutely required for gaming. |
| DirectX Diagnostics Tool | Keep. Useful for troubleshooting. |
| MIDI Audio Support | Keep. Many games use MIDI. |
| OpenGL Support | Keep. Required for OpenGL games (Quake, Half-Life, etc.). |
| Windows Media Player | Keep. Some games use WMP components. Install WMP 10 later via Windows Update. |
| Windows Picture and Fax Viewer | Keep. Useful for viewing screenshots. |
| Windows Sounds | Keep. Default sound scheme. |

### Network — REMOVE

| Component | Action | Reason |
|---|---|---|
| Active Directory Services | Remove | No AD on a gaming workstation. |
| Client for Netware Networks | Remove | No Novell network. |
| Communication Tools | Remove | MS Chat, Phone Dialer, Hyperterminal. Not needed. |
| Control Test Terminal Program | Remove | Not needed. |
| FrontPage Extensions | Remove | No FrontPage. |
| Internet Connection Wizard | Remove | Not using the wizard. |
| MSN Explorer | Remove | Useless. |
| Netmeeting | Remove | Not needed. |
| MSMail and MAPI | Remove | No MS Mail. |
| MAC Bridge | Remove | Not bridging connections. |
| Map Network Drives Wizard | Remove | Can map drives manually. |

### Network — KEEP

| Component | Reason |
|---|---|
| Dial-up and VPN Support | Keep if using dial-up or VPN. Remove if not. |
| Internet Explorer | Keep. Required for Windows Update and many installers. |
| Internet Explorer Core | Keep. Critical. Many services depend on it including activation. |
| IIS (Internet Information Services) | Remove. No web serving on a gaming workstation. |
| IP Conferencing | Keep if using NetMeeting-style conferencing. Otherwise remove. |
| Network Address Translation (NAT) | Remove if not sharing internet. Keep if using ICS. |

### Operating System Options — REMOVE

| Component | Action | Reason |
|---|---|---|
| Blaster/Nachi Removal Tools | Remove | Worm removal tools, not needed. |
| Manual Install and Upgrade | Remove | Not needed for clean install. |
| Out of Box Experience (OOBE) | Remove | Not needed for workstation. |
| Tour | Remove | XP tour. Not present on Server 2003 but check. |
| Windows Messenger | Remove | Not needed. |
| MSN Messenger | Remove | Not needed. |
| Tablet PC components | Remove | No tablet. |

### Operating System Options — KEEP

| Component | Reason |
|---|---|
| XP/2003 Antivirus | Keep. Some installers check for it. |
| Disk Cleanup | Keep. Useful. |
| DR Watson | Keep. Debugger, useful for troubleshooting. |
| File System Verification Utility | Keep. Useful. |
| Help and Support | Keep. Some components depend on it. |
| Open GL | Keep. Required for gaming. |
| Package Installer | Keep. Required for updates. |
| Remote Desktop | Keep if you want RDP access to the machine. |
| Search Assistant | Keep. Useful for finding files. |
| System Restore | Remove. Not present on Server 2003 by default. |
| Task Scheduler | Keep. Useful. |
| Windows File Protection | Keep. Prevents critical file corruption. |
| Windows Update | Keep. Needed for post-install updates. |

### Directories — REMOVE

| Component | Action | Reason |
|---|---|---|
| DOCS folder | Remove | Windows documentation. Not needed. |
| SUPPORT folder | Remove | Support tools. Not needed. |
| VALUEADD folder | Remove | Optional components. Not needed. |
| WIN9XMIG, WINNTUPG, WIN9XUPG folders | Remove | Upgrade migration tools. Clean install only. |

---

## Part 3: What to Setup and Configure

### Unattended Setup

Configure these in nLite's "Unattended" section:

| Setting | Value |
|---|---|
| Product Key | Your Server 2003 R2 Standard key |
| Organization Name | Your name |
| Computer Name | RETRO-PC (or your choice) |
| Admin Password | Set or leave blank (no password) |
| Display Resolution | 1024x768, 32-bit, 75Hz |
| Network Configuration | Typical settings (automatic IP) |
| Workgroup | WORKGROUP |
| Time Zone | Your timezone |
| Regional Settings | Your region/keyboard |
| Accept EULA | Yes |
| Skip Activation | Yes (if using volume license) |
| Skip OOBE | Yes |
| Auto logon | Administrator (or your user) |

### Services Configuration

Configure these in nLite's "Services" section. This is the most important part for workstation conversion:

#### Services to DISABLE

| Service | Why |
|---|---|
| Alerter | Not needed on workstation. |
| Computer Browser | Not needed. |
| Distributed File System (DFS) | Server feature. |
| Distributed Link Tracking Client | Server feature. |
| Error Reporting Service | Annoying, not needed. |
| Help and Support | Can re-enable if needed. |
| IIS Admin | No web server. |
| IPSec Services | Not needed for workstation. |
| Net Logon | No domain. |
| NT LM Security Support Provider | Not needed. |
| Remote Registry | Security risk, not needed. |
| Server | No file sharing as server. |
| Smart Card | No smartcard reader. |
| TCP/IP NetBIOS Helper | Can disable if not using NetBIOS. |
| WebClient | WebDAV, not needed. |
| Windows Time | Can disable if not syncing clock. |
| WMI Performance Adapter | Not needed. |

#### Services to ENABLE (set to Automatic)

| Service | Why |
|---|---|
| Windows Audio | Disabled by default on Server 2003. Critical for gaming. |
| Themes | Only if you want Luna theme. Skip for performance. |
| Windows Image Acquisition (WIA) | If using scanners/cameras. |
| Plug and Play | Ensure Automatic (usually already on). |
| Windows Management Instrumentation | Keep enabled. Many tools depend on it. |

#### Services to set to MANUAL

| Service | Why |
|---|---|
| Automatic Updates | Manual. Trigger manually if needed. |
| Background Intelligent Transfer | Manual. Used by Windows Update. |
| Print Spooler | Manual. Only start when printing. |
| TCP/IP NetBIOS Helper | Manual if you use network browsing. |
| Workstation | Manual. Needed for network access. |

### Registry Tweaks

Configure these in nLite's "Tweaks" section:

#### Performance

| Tweak | Value | Why |
|---|---|---|
| Processor Scheduling | Programs | Server 2003 defaults to background services. Must change for workstation. |
| Memory Usage | Programs | Same reason. |
| Disable Paging Executive | No | 256MB is too little to disable paging. |
| Visual Effects | Best Performance | Strips all visual effects for maximum speed. |

#### Desktop and Appearance

| Tweak | Value | Why |
|---|---|---|
| Theme | Classic | Luna wastes RAM. Classic theme only. |
| Desktop Background | None | Saves memory. |
| Screensaver | None | Not needed. |
| Show My Documents on Desktop | Yes | Personal preference. |
| Show My Computer on Desktop | Yes | Personal preference. |

#### Security

| Tweak | Value | Why |
|---|---|---|
| IE Enhanced Security | Disabled | Most annoying Server 2003 default. |
| Shutdown Event Tracker | Disabled | Do not want to explain every shutdown. |
| Windows Firewall | Enabled | Keep for basic protection. |
| Simple File Sharing | Enabled | Easier networking. |

#### Network

| Tweak | Value | Why |
|---|---|---|
| NetBIOS over TCP/IP | Enabled | Needed for workgroup browsing. |
| LMHOSTS lookup | Disabled | Not using LMHOSTS files. |

#### System

| Tweak | Value | Why |
|---|---|---|
| Disable NTOSIMG | Yes | Saves boot time. |
| Boot Timeout | 5 seconds | Faster boot. |
| Disable Last Known Good | No | Keep as recovery option. |
| Disable SFC (Windows File Protection) | No | Keep SFC enabled. Prevents corruption. |
| Prefetch | Enabled | Speeds app launch. |
| Disable DLL Caching | No | Keep for performance. |

### Patches

nLite can apply specific patches:

| Patch | Action | Why |
|---|---|---|
| SFC disabled | Do NOT apply | Keep Windows File Protection. |
| TCP connections limit patch | Apply | Server 2003 has a limit on half-open TCP connections. Patch removes it. Important for games and networking. |
| Unsigned drivers | Allow | Many retro drivers are unsigned. Saves prompts. |
| Uxtheme patch | Apply only if keeping Luna theme. | Not needed with Classic theme. |

---

## Part 4: Build the ISO

After configuring all settings:

1. In nLite, proceed to the final step
2. Select "Bootable ISO" as the output method
3. Mode: Create Image
4. Burn options: DAO (Disc-at-Once) if burning to CD
5. Output: Save ISO to disk, then burn with ImgBurn or your preferred tool
6. Alternatively: Write the ISO to a USB flash drive using Rufus (select "Windows XP" mode for proper bootability)

---

## Part 5: Post-Install Checklist

After installing from the nLite-customized ISO:

- [ ] Install GPU drivers (XP drivers for your graphics card)
- [ ] Install sound card drivers (XP Sound Blaster drivers)
- [ ] Install chipset drivers (if needed for i440BX/i815)
- [ ] Install DirectX 9.0c (if not integrated as addon)
- [ ] Run dxdiag, verify DirectDraw, Direct3D, AGP Texture, sound acceleration all enabled
- [ ] Set hardware acceleration to Full (Display Properties > Advanced > Troubleshoot)
- [ ] Verify Windows Audio service is running
- [ ] Test a game

---

## Summary: Conservative vs Aggressive Strip

### Conservative (Recommended for First Build)

Remove only clearly unnecessary items. Keep more for compatibility. Larger ISO, more reliable.

- Remove: server applications, ISDN/modems/scanners/printers/wireless, MSN/Messenger, old drivers, Luna theme, screensavers, sample media
- Keep: all multimedia (DirectX, OpenGL, WMP, MIDI, codecs), all network (IE, Ethernet, TCP/IP), all system tools

### Aggressive (For Minimum Size)

Remove everything in the remove lists above. Smaller ISO, less RAM usage, but higher risk of compatibility issues with specific games.

- Also remove: Help and Support, Search Assistant, Remote Desktop, WIA, printer support, games, calculator
- Risk: some installers may fail, some games may miss dependencies
- Only do this if you are confident and willing to troubleshoot

**Recommendation:** Start with Conservative. If the system works and you want to strip further, rebuild the ISO with Aggressive settings and reinstall.