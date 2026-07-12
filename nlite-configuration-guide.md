# nLite Configuration Guide for Server 2003 R2 Gaming Workstation

> Target: Windows Server 2003 R2 Standard (32-bit), Disc 1 (SP1 base)
> Hardware: Slot 1 Pentium III, 500MHz, 256MB RAM, 128GB IDE
> nLite version: 1.4.9.3
> Goal: Strip server components, enable multimedia, maximize free RAM for gaming

---

## Important: Server 2003 is NOT Windows XP

Server 2003 (NT 5.2) shares the XP codebase but ships with a different component tree. Many components that XP nLite guides tell you to remove DO NOT EXIST in Server 2003:

- No Pinball
- No XP screensavers (screensaver support exists, but the XP-themed screensavers are not included)
- No System Restore
- No Fast User Switching
- No Welcome Screen / OOBE
- No XP Tour
- No MSN Explorer (in most Server 2003 editions)

Do NOT follow XP nLite removal guides for Server 2003. The component tree is different. This guide covers what nLite actually presents for Server 2003.

---

## Part 1: What to Integrate

### Service Pack

Integrate **Server 2003 SP2** onto the SP1 base on Disc 1. SP2 supersedes SP1 — no need to slipstream SP1 first.

- Download: Windows Server 2003 Service Pack 2 (32-bit x86) from Microsoft Update Catalog or archive sites
- In nLite: select "Service Pack" task, point to the SP2 installer

### Hotfixes

Optional. If you want post-SP2 security updates, download individual hotfixes from the Microsoft Update Catalog and integrate via the "Hotfixes" task. For a retro gaming machine that will not be on the internet 24/7, this is low priority. The May 2019 final security rollup is the last worthwhile update.

Alternatively, use the Legacy Update tool after install to pull updates from the catalog.

### Drivers

Do NOT integrate GPU, audio, or chipset drivers through nLite. Install them post-OS.

The only driver integration that may be necessary is a mass storage / IDE controller driver — but only if the motherboard uses a non-standard controller. For i440BX or i815 with standard PATA, the default Microsoft drivers work fine.

### Addons (Recommended)

- **DirectX 9.0c redistributable** — integrate as an addon so DirectX is present at first boot. Saves a manual step.
- **.NET Framework 2.0** — if you skipped R2 Disc 2 but want .NET support, integrate as an addon.

---

## Part 2: What to Remove

nLite's component tree for Server 2003 is server-oriented. The goal is to remove server-specific components while keeping everything needed for gaming and multimedia.

### Server Components — REMOVE

These are the components that make Server 2003 a server and are useless for a gaming workstation:

| Component | Why Remove |
|---|---|
| Internet Information Services (IIS) | Web/FTP server. Not needed. |
| IIS Common Files | IIS support files. |
| IIS Manager | IIS administration tool. |
| SMTP Service | Mail server. Not needed. |
| FTP Service | FTP server. Not needed. |
| NNTP Service | News server. Not needed. |
| World Wide Web Service | HTTP server. Not needed. |
| Active Directory Services | Domain controller. Not needed on workstation. |
| Certificate Services | PKI / CA. Not needed. |
| Remote Installation Services (RIS) | PXE boot / OS deployment. Not needed. |
| Windows File Services / DFS | Distributed filesystem. Not needed. |
| Terminal Server | Application mode terminal services (not Remote Desktop for Administration). |
| Microsoft Message Queue (MSMQ) | Message queuing. Not needed. |
| Windows Media Services | Streaming media server. Not needed. |
| UDDI Services | Universal Description, Discovery and Integration. Not needed. |
| Windows Services for UNIX | Interoperability with UNIX. Not needed. |
| WMI SNMP Provider | SNMP monitoring. Not needed. |
| Fax Service | Fax server. Not needed. |
| Telnet Client | Not needed. |
| Telnet Server | Not needed. |
| Windows Management Instrumentation (WMI) | Keep this — many tools depend on it. Do NOT remove. |
| Manage Your Server | Server configuration wizard. Removed via tweaks, not component removal. |

### Network Components — REMOVE (Server-Specific)

| Component | Why Remove |
|---|---|
| Client for NetWare Networks | No Novell network. |
| Gateway Service for NetWare | No Novell gateway. |
| IP Security (IPSec) | Not needed for workstation. |
| Simple TCP/IP Services | Echo/discard — not needed. |
| Network Monitor | Packet capture. Not needed. |
| Connection Manager Administration Kit | VPN profile builder. Not needed. |

### Network Components — KEEP

| Component | Why Keep |
|---|---|
| Internet Explorer | Required for Windows Update and many installers. |
| Internet Explorer Core | Critical. Many services depend on it including activation. Do NOT remove. |
| TCP/IP | Required for networking. |
| Ethernet (LAN) drivers | Required. |
| File and Printer Sharing | Keep if you want to share files. Remove if not. |
| Client for Microsoft Networks | Keep. Needed for workgroup access. |
| Remote Desktop | Keep if you want RDP access to the machine. |
| Dial-up and VPN | Keep if using. Remove if not. |

### Drivers — REMOVE (Hardware Not Present)

These are generic Windows drivers for hardware you do not have. Remove them.

| Component | Why Remove |
|---|---|
| ATM (Asynchronous Transfer Mode) | No ATM hardware. |
| Bluetooth Support | No Bluetooth on a PIII. |
| Cameras and Camcorders | Install manually if needed. |
| ISDN | No ISDN hardware. |
| Modems | No dial-up modems. |
| Multifunctional (combo cards) | No combo cards. |
| Portable Audio | No portable audio players. |
| Printers | Install printer drivers manually if needed. |
| SCSI/RAID | No SCSI/RAID. (Keep if using CD-ROM emulation like Daemon Tools.) |
| Smartcards | No smartcard reader. |
| Sound Controllers | You will install Sound Blaster drivers manually. |
| Tape Drives | No tape drives. |
| Wireless Ethernet (WLAN) | No wireless on Slot 1 PIII. |
| Sony Memory Stick | No Memory Stick hardware. |
| Gravis Digital Game Port | No Gravis gameport. |
| Serial Pen Tablet | No tablet. |
| IBM ThinkPad | Not a ThinkPad. |
| IBM PS2 Track Point | No trackpoint. |
| Infrared | No IR hardware. |

### Drivers — KEEP

| Component | Why Keep |
|---|---|
| Display Drivers (default VGA) | Required for setup. You will install GPU drivers manually. |
| Ethernet (LAN) | Required for networking. |
| Floppy Support | Keep if you have a floppy drive. Needed for boot disks. |
| Firewire (1394) | Keep if you have Firewire ports. |
| Joystick Support | Keep if using gameport joysticks. |
| Logical Disk Manager | Keep. Needed for drive management. |
| Ports (COM & LPT) | Keep. Needed for serial/parallel devices. |
| USB Audio support | Keep if using USB audio devices. |
| USB Ethernet | Keep if using USB Ethernet adapters. |

### Hardware Support — REMOVE

| Component | Why Remove |
|---|---|
| AGP Filters | Not needed on i440BX with standard AGP. |
| ALI 1535 SMBus | No ALI chipset. |
| ALI IDE Controller | No ALI chipset. |
| Battery | Desktop, not laptop. |
| CMD PCI IDE Controller | No CMD controller. |
| CPU AMD | Intel PIII. |
| CPU Transmeta Crusoe | Not Transmeta. |
| IEEE 1284.4 Devices (Dot4) | No Dot4 devices. |
| Iomega Zip Drive | No Zip drive. |
| Microsoft Color Management (ICM) | Not critical for gaming. |
| Modem Support | No modems. |
| Multi-port serial adapters | No multi-port serial. |
| PCMCIA | Desktop, no PCMCIA. |
| Ramdisk | Not needed. |
| Secure Digital Host Controller | No SD card slot. |
| Toshiba PCI IDE Controller | No Toshiba controller. |
| Toshiba DVD decoder card | No Toshiba decoder. |
| USB Video Capture | No USB video capture. |
| Windows CE USB Host | No WinCE devices. |
| Brother Devices | No Brother hardware. |
| ATM Support | No ATM network. |

### Hardware Support — KEEP

| Component | Why Keep |
|---|---|
| CPU Intel | Required for Intel PIII. |
| Firewire Network Support | Keep if using Firewire networking. |
| Intel PCI IDE Controller | Required for i440BX/i815 IDE. |
| Multi Processor Support | Keep if your board is dual-socket. Harmless if single. |
| Printer Support | Keep if you ever want to install a printer. |
| Windows Image Acquisition (WIA) | Keep if using scanners/cameras. Otherwise remove. |

### Multimedia — KEEP ALL

Server 2003 does not ship with the consumer multimedia bloat that XP has. What it does have is essential for gaming:

| Component | Why Keep |
|---|---|
| DirectX | Absolutely required for gaming. |
| DirectX Diagnostics Tool (dxdiag) | Required for troubleshooting. |
| OpenGL Support | Required for OpenGL games (Quake, Half-Life, Unreal, etc.). |
| ACM Core Codecs | Required for audio. |
| MIDI Audio Support | Many games use MIDI. |
| Windows Media Player | Some games use WMP components. Can upgrade to WMP 10 via Windows Update. |
| Windows Media Player 6.4 | Keep — some games specifically require mplayer2.exe. |
| Windows Sounds | Default sound scheme. Keep. |
| Windows Picture and Fax Viewer | Useful for viewing screenshots. |

### Multimedia — REMOVE (If Present)

| Component | Why Remove |
|---|---|
| Luna Theme | XP visual style engine. Not present on Server 2003 by default. If nLite offers it, remove it. Classic theme only for performance. |
| Mouse Cursors | Extra cursor themes (3D, bronze, etc.). Keep default white. |
| Music Samples | Useless sample audio. |
| Movie Maker | Not present on Server 2003. If it appears, remove. |
| Speech Support | Not needed for gaming. |
| Tablet PC components | Not present on Server 2003. If it appears, remove. |
| Media Center | Not a Media Center distribution. |
| Intel Indeo Codecs | Old video codec. Install later if a specific game needs it. |

### Directories — REMOVE

| Directory | Why Remove |
|---|---|
| DOCS | Windows documentation. Not needed. |
| SUPPORT | Support tools. Not needed. |
| VALUEADD | Optional components. Not needed. |
| WIN9XMIG | 9x migration. Clean install only. |
| WINNTUPG | NT upgrade. Clean install only. |
| WIN9XUPG | 9x upgrade. Clean install only. |

---

## Part 3: What to Setup and Configure

### Unattended Setup

| Setting | Value |
|---|---|
| Product Key | Your Server 2003 R2 Standard key |
| Organization Name | Your name |
| Computer Name | RETRO-PC (or your choice) |
| Admin Password | Set or leave blank |
| Display Resolution | 1024x768, 32-bit, 75Hz |
| Network Configuration | Typical settings (automatic IP) |
| Workgroup | WORKGROUP |
| Time Zone | Your timezone |
| Accept EULA | Yes |
| Skip Activation | Yes (if volume license) |
| Auto logon | Administrator or your user |

### Services Configuration

This is the most critical part of the workstation conversion. Server 2003 defaults many services to Automatic that a workstation does not need.

#### Services to DISABLE

| Service | Why |
|---|---|
| Alerter | Not needed on workstation. |
| Computer Browser | Not needed. |
| Distributed File System (DFS) | Server feature. |
| Distributed Link Tracking Client | Server feature. |
| Error Reporting Service | Annoying, not needed. |
| Fax | No fax hardware. |
| IIS Admin | No web server. |
| IPSec Services | Not needed for workstation. |
| Net Logon | No domain. |
| Network Monitor | Packet capture, not needed. |
| NT LM Security Support Provider | Not needed. |
| Remote Installation | No RIS. |
| Remote Registry | Security risk, not needed. |
| Server | No file sharing as server. |
| Simple TCP/IP Services | Echo/discard. Not needed. |
| Smart Card | No smartcard reader. |
| SNMP | No SNMP monitoring. |
| Telnet | Not needed. |
| WebClient | WebDAV. Not needed. |
| Windows Time | Can disable if not syncing clock. |
| WMI Performance Adapter | Not needed. |

#### Services to ENABLE (Automatic)

| Service | Why |
|---|---|
| Windows Audio | Disabled by default on Server 2003. CRITICAL for gaming. |
| Windows Image Acquisition (WIA) | If using scanners/cameras. |
| Plug and Play | Ensure Automatic (usually already on). |
| Windows Management Instrumentation | Keep. Many tools depend on it. |
| Event Log | Keep. Some tools depend on it. |
| Cryptographic Services | Keep. Needed for driver signing and Windows Update. |

#### Services to set to MANUAL

| Service | Why |
|---|---|
| Automatic Updates | Trigger manually if needed. |
| Background Intelligent Transfer | Used by Windows Update. |
| Print Spooler | Only start when printing. |
| TCP/IP NetBIOS Helper | If using network browsing. |
| Workstation | Needed for network access. |
| Removable Storage | Only if using tape/backup devices. |

### Registry Tweaks

#### Performance (CRITICAL)

| Tweak | Value | Why |
|---|---|---|
| Processor Scheduling | Programs | Server 2003 defaults to background services. Must change for workstation. |
| Memory Usage | Programs | Same reason. |
| Visual Effects | Best Performance | Strips all visual effects for maximum speed. |
| Prefetch | Enabled | Speeds application launch. |
| Boot Timeout | 5 seconds | Faster boot. |
| Disable Paging Executive | No | 256MB is too little to disable paging. |

#### Desktop and Appearance

| Tweak | Value | Why |
|---|---|---|
| Theme | Classic | Luna wastes RAM (if present). Classic theme only. |
| Desktop Background | None | Saves memory. |
| Screensaver | None | Not needed for a retro gaming machine. |

#### Security

| Tweak | Value | Why |
|---|---|---|
| IE Enhanced Security Configuration | Disabled | Most annoying Server 2003 default. Blocks all web browsing. |
| Shutdown Event Tracker | Disabled | Do not want to explain every shutdown reason. |
| Manage Your Server page | Disabled | Server configuration wizard. Not needed. |
| Windows Firewall | Enabled | Keep for basic protection if on network. |
| Simple File Sharing | Enabled | Easier networking. |

#### Network

| Tweak | Value | Why |
|---|---|---|
| NetBIOS over TCP/IP | Enabled | Needed for workgroup browsing. |
| LMHOSTS lookup | Disabled | Not using LMHOSTS. |

### Patches

| Patch | Action | Why |
|---|---|---|
| TCP connections limit patch | Apply | Server 2003 limits half-open TCP connections. Patch removes the limit. Important for games and networking. |
| Unsigned drivers | Allow | Many retro drivers are unsigned. Saves prompts during driver installation. |
| SFC (Windows File Protection) | Do NOT disable | Keep SFC. Prevents critical file corruption. |
| Uxtheme patch | Skip | Not needed with Classic theme. |

---

## Part 4: Build the ISO

1. In nLite, proceed to the final step
2. Select "Bootable ISO" as output
3. Mode: Create Image
4. Save ISO to disk
5. Burn with ImgBurn (DAO mode) or write to USB with Rufus (select "Windows XP" boot mode for proper bootability)

---

## Part 5: Post-Install Checklist

After installing from the nLite-customized ISO:

- [ ] Install GPU drivers (XP drivers for your graphics card)
- [ ] Install sound card drivers (XP Sound Blaster drivers)
- [ ] Install chipset drivers (if needed)
- [ ] Install DirectX 9.0c (if not integrated as addon)
- [ ] Run dxdiag — verify DirectDraw, Direct3D, AGP Texture Acceleration, sound acceleration all enabled
- [ ] Set hardware acceleration to Full (Display Properties > Settings > Advanced > Troubleshoot)
- [ ] Verify Windows Audio service is running
- [ ] Verify IE Enhanced Security is disabled
- [ ] Verify Shutdown Event Tracker is disabled
- [ ] Verify performance is set to Programs (not background services)
- [ ] Test a game

---

## Summary

The Server 2003 nLite strategy is the inverse of XP. XP guides strip consumer bloat (MSN, Messenger, Tour, Pinball, screensavers). Server 2003 does not have those. Instead, you strip server infrastructure (IIS, Active Directory, DFS, Certificate Services, RIS, MSMQ, Media Services, Telnet) and configure server services to workstation defaults (disable server services, enable audio, switch performance to Programs).

The multimedia stack (DirectX, OpenGL, codecs, MIDI, WMP) must be kept — it is what enables gaming. The network stack (IE, TCP/IP, Ethernet, Client for Microsoft Networks) must be kept — it is needed for updates and file transfer.