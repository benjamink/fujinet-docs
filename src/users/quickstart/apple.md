# Apple II & III Quickstart Guide

## FujiApple: the Apple II & III FujiNet

The "production ready" version of the FujiApple, Rev1, was released in June 2023. A SmartPort-enabled Apple II/III is required for most functions (see below for expansion card combinations that provide SmartPort functionality for non-SmartPort-native Apples).

FujiNet emulates the following devices:

- **SmartPort drives** (HDV, PO, 2MG)
- **Disk II** (WOZ, PO [140K], DSK)
- **CP/M** (via [RunCPM](https://github.com/MockbaTheBorg/RunCPM))
- **Clock**
- **Modem**
- **Printer**

The clock, modem, and printer are presented as SmartPort devices, and therefore require software or ROM support (for example, printer support is available via a custom Apple IIc ROM on that model).

CP/M support is provided through a fully emulated RunCPM environment with storage on the built-in microSD slot.

> **Important:** Rev0 and Rev00 do not support CP/M without a hardware modification to the board, but all other functions are supported.

---

## Getting to Know Your FujiNet

- **Power:** FujiNet draws power from the SmartPort cable and powers on when the Apple is turned on. There is no separate power switch.
- **MicroSD slot:** Push/push MicroSD slot. An SD card is not required for use. The SD card must be formatted **FAT32**; exFAT is not supported.
- **Status LEDs:** White LED indicates WiFi connection; amber/yellow LED indicates bus activity (SmartPort/Disk II).
- **Buttons:** Button A (function to be determined) and Reset.
- **Port:** One IDC20 port for SmartPort/Disk II connections, using a suitable cable or adapter for your interface/controller.

---

## Supported Apple II & III Systems

### Native SmartPort Support

- Apple IIgs
- Apple IIc (SmartPort functionality **not available** on ROM 255)
- Apple IIc+

### With SmartPort Card

- Apple II+
- Apple IIe
- Apple IIe Enhanced/Platinum
- Most clones of the above machines
- Apple III & III+

### SmartPort Cards

#### Extensively Tested and Working

- [KBOOHK softSP Card](https://ct6502.org/product/softsp) v6 (or newer) and I/O Controller / 5.25 Drive Controller Card
- [A2Pico](https://jcm-1.com/product/a2pico/) with [softSP](https://github.com/oliverschmidt/softsp) v6 (or newer) and I/O Controller / 5.25 Drive Controller Card
- Grappler+ with DIY softSP (E)EPROM v6 (or newer) and I/O Controller / 5.25 Drive Controller Card
- SuperSerial with DIY softSP (E)EPROM v5 and I/O Controller / 5.25 Drive Controller Card
  - SSC softSP v5 in slot 5, Disk II in slot 6

> **Warning:** While the original Disk II interface card will work with SoftSP cards to provide SmartPort + Disk II Drive 1 functionality (or Drive 2 if FujiNet is plugged into the second header), the ease with which the IDC20 cable can be offset by one row or column of pins -- which **will cause damage to hardware** -- makes this a less than ideal configuration.
>
> If you only have a Disk II card available to use with your FujiNet, it is advisable to check the alignment of the plug on the header at least twice before powering the equipment up. Make sure **no** pins are visible outside the IDC20 plug.

#### Minimally Tested / Known Issues

- **Apple Liron:** SmartPort mode only, no Disk II mode.
- **[Yellowstone](https://www.bigmessowires.com/yellowstone):** Currently not working for Apple III. Must be used with IDC20 cable only -- no DB19 adapter.

### "Solo" 5.25" Disk Controller Cards (No SoftSP)

- Original Disk II Interface Card with two IDC20 headers (16-sector PROMs only)
- Apple I/O Controller / 5.25 Drive Controller with DB19 connector

---

## Supported Drive Types by Interface Card / Computer

| Card / Computer | SmartPort | Disk II D1 | Disk II D2 |
|---|:---:|:---:|:---:|
| **Disk II Interface Card (IDC20)** | Yes ^1^ | Yes ^2^ | Yes ^2^ |
| **I/O Controller / 5.25 Drive Controller (DB19)** | Yes ^1^ | Yes | Yes |
| **Liron Card (DB19)** | Yes | No | No |
| **Yellowstone (IDC20)** ^3,4^ | Yes | Yes | Yes |
| **Apple IIc ROM 255 (DB19)** | No | No | Yes ^5^ |
| **Apple IIc ROM 0, 3, 4 (DB19)** | Yes | No | Yes |
| **Apple IIc+ (DB19)** | Yes | Yes | Yes |
| **Apple IIgs (DB19)** | Yes | Yes | Yes |
| **Apple III / III+ (IDC26/DB25)** ^6^ | Yes ^7^ | Yes ^2^ | Yes ^2^ |

**Notes:**

1. With SoftSP card or equivalents.
2. Only one Disk II can be emulated, depending on how FujiNet is connected -- Drive 1 when connected to the Apple II Interface Card's Drive 1 header or Apple III/III+'s internal drive header, or Drive 2 when connected to the Apple II Interface Card's Drive 2 header or Apple III/III+'s external port. Load disk image into FujiNet's Disk II Drive 1 slot, which is presented to the interface as the sole emulated Disk II. Apple III/III+ requires a 26-pin to 20-pin adapter.
3. Must be used with IDC20 cable, not DB19 adapter. Yellowstone acts in either SmartPort *or* Disk II mode, not both at the same time.
4. Not currently working for Apple III/III+.
5. When plugged into Apple IIc ROM 255's external DB19 floppy port.
6. Requires [Apple III FujiNet driver](https://github.com/FujiNetWIFI/fujinet-apps/tree/master/drivers/apple3). Internal port is IDC26; external port for Apple III is IDC26, and for Apple III+ is DB25.
7. With SoftSP card (or equivalent) via 26-pin to 20-pin adapter cable or Liron card; supports only the first two SmartPort block devices.

> **Note:** Disk II emulation is currently read-only (as of August 2024).

---

## Supported Image Types by Drive Type

| Drive Type | DSK/DO | WOZ | PO | HDV | 2MG |
|---|:---:|:---:|:---:|:---:|:---:|
| **SmartPort** | Yes ^1^ | No | Yes | Yes | Yes |
| **Disk II** | Yes | Yes ^2^ | Yes ^2^ | No | No |

**Notes:**

1. ProDOS images only.
2. 5.25" images only.

---

## Hooking Up FujiNet

### DB19 Connection (Native SmartPort / DB19 Drive Controller)

For systems with native SmartPort or a DB19 Drive Controller card, you will need a **DB19 to IDC20 adapter** that connects to the FujiNet.

FujiNet Rev1 can emulate a second drive with the custom FujiNet DB19 adapter only when connected to the I/O Controller / 5.25 Drive Controller cards. Older Rev0(00) prototypes and Rev1 without the custom DB19 adapter only support Disk II Drive 1.

> **Caution:** The custom FujiNet DB19 adapter **should not** be used with other devices without first confirming the pinout conforms to the other device's pinout.

### IDC20 Connection (Disk II Interface / Yellowstone)

For systems with an IDC20 header (two rows of ten pins), connect the FujiNet with an IDC20 cable. If using the Disk II card, be sure the cable is plugged in correctly or you could damage the FujiNet.

### Where to Get Adapters

- [FujiNet DB19 to IDC20 adapter](https://mozzwald.com/product-category/appleii/) or [build your own](https://github.com/FujiNetWIFI/fujinet-hardware/tree/master/AppleII)
- BMOW [DB-19 Male Adapter and Extension Cable](https://shop.bigmessowires.com/products/db-19-adapter-and-extension-cable)
- [A2Heaven Adapter](http://www.a2heaven.com/webshop/index.php?rt=product/product&product_id=125)

### Power

FujiNet is powered directly from the Apple II bus -- no external power is required. You can optionally power the FujiNet from the USB port on the board. The USB port is also used for [firmware updates](#updating-firmware) and provides debug output messages to a serial monitor on a computer. The web-based management and WebDAV access to the SD card continue to be accessible if powered from USB while the host Apple II is powered down.

---

## Apple System Specifics

### Apple II+ / IIe

The first time you power on the Apple II, FujiNet will have its CONFIG disk mounted.

1. Press `Ctrl`+`Reset` to get a prompt.
2. Type `PR#X` (where X is whichever slot your DIY SoftSP ROM card is in, for example `PR#5`).
3. Press `Return` to load CONFIG.

See [Navigating CONFIG](#navigating-config) below.

### Apple IIc

1. Plug in the FujiNet.
2. Remove any floppies from the internal 5.25" drive.
3. Power on the Apple. FujiNet will boot CONFIG.

> **Note:** On an Apple IIc with the ROM 255 ($FF), the Apple will not boot the FujiNet, resulting in a failed floppy boot (both PR#6 and PR#7). This is a limitation of the ROM 255 Apple IIc, as it did not have built-in support for SmartPort.

### Apple IIc+

1. Plug in the FujiNet.
2. Remove any floppies from the internal 3.5" drive.
3. Power on the Apple. FujiNet will boot CONFIG.

### Apple IIgs

On the Apple IIgs, you can set the boot device as Port 5 (SmartPort) by entering the Control Panel at boot:

1. Press `Ctrl`+`Open Apple`+`Esc` to open the Control Panel.
2. Choose **Disk** and set startup disk to **Slot 5**.

This prevents the Apple IIgs from asking you to attach a drive when FujiNet is connected.

#### Apple IIgs Keyboard Shortcuts

| Keys | Function |
|---|---|
| `Ctrl`+`Open Apple`+`Reset` | Reboot |
| `Ctrl`+`Shift`+`Open Apple`+`Reset` | Reboot and re-load BRAM (ROM 03) |
| `Ctrl`+`Open Apple`+`Option`+`Reset` | System test (`Open Apple`+`Option` to repeat) |
| `Option` while powering on | Menu to reset standards |
| `Ctrl`+`Option`+`Reset` | Reboot and give menu |
| `Ctrl`+`Open Apple`+`Esc` | Go to Control Panel |
| `Ctrl`+`Open Apple`+`Shift`+`Esc` | More direct to Control Panel |
| `Shift` 5 times | Enable sticky keys (ROM 03 or Sys 6) |
| `Shift`+`Open Apple`+`Clear` | Enable keyboard mouse (ROM 03 or Sys 6) |
| `Ctrl`+`Open Apple`+`Del` | Clear keyboard type-ahead buffer |
| Hold `Open Apple`, then `Ctrl`+`Del` | Auto fire Button 0 |
| `Shift`+`Period` on keypad | Comma |
| `Ctrl`+`Open Apple`+`2` in GS/OS desktop app | Select "About..." |
| `Ctrl`+`6`, then press a key in BASIC | Set cursor to that key |
| `Ctrl`+`Open Apple`+`Option`+`N` at startup screen | Credits |

### Apple III & III+

Two options are available to connect the FujiNet:

- **Liron card** -- Allows SmartPort drives only. Requires a DB19 to 20-pin adapter to connect the FujiNet to the Liron card.
- **SoftSP card or DIY SoftSP on Grappler+** -- FujiNet is connected to either the external or internal drive port with a 26-pin to 20-pin adapter cable.

The driver and some prebuilt 16MB SOS images are available here:
[Apple III FujiNet Driver](https://github.com/FujiNetWIFI/fujinet-apps/tree/master/drivers/apple3)

---

## Navigating CONFIG

The first time you boot FujiNet CONFIG, it will prompt you to connect to WiFi. Select your access point and enter its passphrase. This information is saved internally and, if an SD card is present, is also saved to [fnconfig.ini](https://github.com/FujiNetWIFI/fujinet-platformio/wiki/FujiNet-Configuration-File%3A-fnconfig.ini).

> **Important:** FujiNet is powered by the Espressif ESP32 chipset, which works on **2.4 GHz WiFi networks only**. If you are using a "mixed" 2.4 GHz / 5 GHz WiFi network (both radio bands with the same SSID/network name), you **may** have problems connecting your FujiNet device to the network.

### Main Screen Layout

The CONFIG screen shows **host slots** on the top and **disk slots** on the bottom. Press `Tab` to jump between the host slots and disk slots. Press `Return` on a highlighted host slot to begin selecting and mounting a disk image from that host to an emulated disk drive.

### Host Types

Hosts can be:

- An IP address or hostname of a **TNFS server** (for example: `apps.irata.online`, `fujinet.diller.org`, `tnfs.fujinet.online`, `10.0.27.222`)
- An **SMB** or **FTP** server URL in the form `SMB://server.address` or `FTP://server.address` -- both require anonymous access; `server.address` can be an IP address or domain name
- **`SD`** -- points to the onboard SD card socket

### Editing Hosts

With a host entry selected, press `E` to edit it. Host entries can be up to 30 characters long.

### Mounting a Disk Image

1. Select the desired host and then a disk image file.
2. When prompted, select a disk drive. The first four drives are SmartPort devices; the last two are Disk II Drive 1 and Drive 2.
3. Choose to mount it **Read Only** (press `R` or `Return`) or **Read/Write** (press `W`).

> **Caution:** Most if not all public-facing TNFS servers do not allow write access, so you should mount them as read-only.

> **Note:** Disk II emulation in FujiNet is read-only (as of August 2024), so mount those disks accordingly.

### Booting

When returned to the main CONFIG screen, press `Esc` to reset the Apple II. For systems that need it, press `Ctrl`+`Reset` and type `PR#X` (where X is the slot of your DIY SoftSP ROM card, or the slot of the physical drive interface card to boot from the Disk II emulated drive).

---

## Web User Interface

FujiNet provides a web-based configuration interface accessible from any browser on the same network.

### Finding Your FujiNet's IP Address

- Use the **Show Config** option (press `C`) from the main CONFIG screen.
- Check your router's list of connected devices.
- Navigate to the default hostname: [http://fujinet.local](http://fujinet.local)

Once you know the IP address, visit `http://<IP_ADDRESS>/` in your browser (for example, `http://192.168.0.222/`).

You can change the FujiNet hostname if desired.

> **Note:** Not all components of the web UI work with Apple II at this point.

---

## Updating Firmware

Download the FujiNet-Flasher (available for Windows, macOS, and Linux) from [https://fujinet.online/download/](https://fujinet.online/download/).

For more information, see [FujiNet-Flasher](https://github.com/FujiNetWIFI/fujinet-platformio/wiki/FujiNet-Flasher).

---

## Finding and Loading Software

Apple II disk images are available on various TNFS servers:

- `tnfs.fujinet.online`
- `fujinet.diller.org`
- `apps.irata.online`

> **Note:** Host names can be entered in lowercase on the Apple II. They will appear in UPPER CASE when the CONFIG app is reloaded. This is normal.

---

## Apple Disk Types, Filesystems, and Slots

This section provides technical background on Apple II disk image formats and how they relate to FujiNet's drive emulation.

### The Relationship Between Sector Order and Filesystem

The FujiNet codebase currently categorizes disk images by sector order:

- `MEDIATYPE_DO` -- DOS 3.3 sector order
- `MEDIATYPE_DSK` -- Ambiguous (could be either order)
- `MEDIATYPE_PO` -- ProDOS sector order
- `MEDIATYPE_WOZ` -- WOZ format

However, what actually determines where a disk image can be mounted (Disk II vs. SmartPort) is the **filesystem**, not the sector order. The sector order can always be corrected on the fly.

### Proposed Media Type Model

A more correct model would distinguish between filesystem presence and sector order:

| Media Type | Description | Mountable As |
|---|---|---|
| `MEDIATYPE_WOZ` | WOZ format | Disk II |
| `MEDIATYPE_DO` | .DSK or .DO 140K, DOS 3.3 sector order, no ProDOS filesystem | Disk II |
| `MEDIATYPE_DO_PF` | .DSK or .DO 140K, DOS 3.3 sector order, ProDOS filesystem | Disk II or SmartPort |
| `MEDIATYPE_PO` | .DSK or .PO 140K, ProDOS sector order, no ProDOS filesystem | Disk II |
| `MEDIATYPE_PO_PF` | .DSK or .PO 140K, ProDOS sector order, ProDOS filesystem | Disk II or SmartPort |
| `MEDIATYPE_HDV` | .PO or .HDV >140K, ProDOS sector order, ProDOS filesystem | SmartPort |

The key insight is that SmartPort can only mount images with a ProDOS filesystem, while Disk II can mount any 140K image regardless of filesystem.

### Apple II Slots and FujiNet Drive Slots

#### Historical Background

The original Apple II has 8 slots (0-7). Slot 0 is limited and slot 7 is extended. The conventional arrangement that developed over time:

- **Slots 1-2:** Character I/O (printers, serial/modems)
- **Slot 6:** Disk II (became a de-facto standard)
- **Slot 7:** SmartPort (allows quick autoboot for daily use while still permitting `PR#6` to boot software that expects Disk II in slot 6)

The autostart ROM searches for a bootable device starting from slot 7 and working downward. SmartPort can quickly determine if a bootable drive is connected, while the Disk II requires a long time to determine that no disk is present.

#### What This Means for FujiNet

FujiNet cannot know or presume any slot location of the Disk II card or SmartPort card in the Apple II. The only meaningful terminology for FujiNet users is:

- **Disk II**
  - Drive 1
  - Drive 2
- **SmartPort**
  - Drive 1
  - Drive 2
  - Drive 3
  - Drive 4

#### Yellowstone SmartPort and Disk II Modes

The Yellowstone is a single firmware in a single expansion slot, unlike the Disk II + SoftSP combination which occupies two slots. Key considerations:

- In SmartPort mode, Yellowstone handles Disk II internally and presents Disk II drives as 140K SmartPort devices to ProDOS. This differs from native Disk II handling (for example, ProDOS 2.5's improved Disk II support is not available through Yellowstone without a firmware update).
- Switching between ProDOS and DOS 3.3 requires switching Yellowstone between SmartPort mode and Disk II mode.

#### Number of SmartPort Drives

ProDOS 8 has a strict limit of **2 drives per slot**. To leverage additional SmartPort drives, ProDOS 8 remaps them to phantom slots. This process is limited to one phantom slot, so ProDOS 8 supports a maximum of **4 SmartPort drives**. GS/OS does not have this limitation and may support more SmartPort drives.

---

## Apple IIc ROM Versions

To determine your ROM version, press `Ctrl`+`Reset` immediately after boot, then type:

```
PRINT PEEK(64447)
```

Reference: [apple2faq.com -- Apple IIc ROM Versions](https://www.apple2faq.com/apple2faq/apple-c-rom-versions/)

### Original IIc (ROM 255, $FBBF = $FF)

- Can use the IIc external drive only
- No AppleTalk firmware
- `PR#7` boots the second drive
- Mouse firmware maps to slot 4
- Serial firmware does not mask incoming linefeed characters
- Serial firmware does not support XON/XOFF protocol

### 3.5 ROM IIc (ROM 0, $FBBF = $00)

- Can use the IIc external drive and the UniDisk 3.5 drive
- AppleTalk firmware maps to slot 7
- `PR#7` returns the message "AppleTalk Off Line"
- Mouse firmware maps to slot 4
- Serial firmware defaults to mask all incoming linefeed characters
- Serial firmware supports XON/XOFF protocol

### Original "Memory-Expandable" IIc (ROM 3, $FBBF = $03)

- Can use the IIc external drive, the UniDisk 3.5 drive, and the IIc Memory Expansion Card
- Mouse firmware maps to slot 7
- No AppleTalk firmware
- `PR#7` kills the system
- Serial firmware defaults to mask all incoming linefeed characters
- Serial firmware supports XON/XOFF protocol

### Revised "Memory-Expandable" IIc (ROM 4, $FBBF = $04)

- Same as the Original "Memory-Expandable" IIc, plus:
- Keyboard buffering firmware bug fixed
- Firmware returns correct information when the Memory Expansion Card is not present

### Apple IIc Plus (ROM 5, $FBBF = $05)

- Can use the external IIc drive, the UniDisk 3.5 drive, and Apple 3.5 drives, but **not** the original IIc Memory Expansion Card
- Contains a Memory Expansion Card connector
- 3.5" internal drive replaces 5.25" internal drive
- Mouse maps to slot 7
- `PR#7` kills the system
- 4 MHz 65C02 microprocessor
- Accelerator chip and static RAM cache permit operation up to 4 MHz
- Keyboard replaced with Apple Standard Keyboard (minus numeric keypad)
- Internal power supply
- Internal modem connector
- Serial ports refitted with mini-DIN 8 connectors
- Headphone jack removed
- Volume control relocated above the keyboard
- 40/80 column switch replaced by keyboard (Sholes/Dvorak) switch

---

## ProDOS Versions

The best ProDOS version to use is the latest, currently **2.4.3**, available at [prodos8.com/releases](https://prodos8.com/releases/).

### Previous (Apple) ProDOS Versions

Available at [mirrors.apple2.org.za](https://mirrors.apple2.org.za/Apple%20II%20Documentation%20Project/Software/Operating%20Systems/Apple%20ProDOS/Disk%20Images/).

| Version | Date | Size (Blocks) |
|---|---|---|
| ProDOS 1.0 | Aug 1983 | 28 |
| ProDOS 1.0 | Sep 1983 | 30 |
| ProDOS 1.0 | Oct 1983 | 30 |
| ProDOS 1.0 | Nov 1983 | 28 |
| ProDOS 1.0.1 | Jan 1984 | 30 |
| ProDOS 1.0.2 | Feb 1984 | 30 |
| ProDOS 1.1 | Aug 1984 | 29 |
| ProDOS 1.1.1 | Sep 1984 | 29 |
| ProDOS 8 1.2 exp 04 | Mar 1986 | 30 |
| ProDOS 8 1.2 | Sep 1986 | 31 |
| ProDOS 8 1.3 | Dec 1986 | 31 |
| ProDOS 8 1.4 B | Feb 1987 | 31 |
| ProDOS 8 1.4B | Feb 1987 | 31 |
| ProDOS 8 1.4 | Apr 1987 | 31 |
| ProDOS 8 1.5 | Apr 1988 | 31 |
| ProDOS 8 1.6 | Jun 1988 | 31 |
| ProDOS 8 1.7B | Aug 1988 | 31 |
| ProDOS 8 1.7 | Aug 1988 | 31 |
| ProDOS 8 1.8 | May 1989 | 31 |
| ProDOS 8 1.9 | Jul 1990 | 33 |
| ProDOS 8 2.0 | Jan 1992 | 34 |
| ProDOS 8 2.0.1 | Mar 1992 | 34 |
| ProDOS 8 2.0.2 | Nov 1992 | 34 |
| ProDOS 8 2.0.3 | May 1993 | 34 |

### BASIC.SYSTEM Versions

| Version | Date |
|---|---|
| ProDOS BASIC 1.0 | Sep 1983 |
| ProDOS BASIC 1.0 | Nov 1983 |
| ProDOS BASIC 1.1 | Jun 1984 |
| ProDOS BASIC 1.2 | Dec 1987 |
| ProDOS BASIC 1.3 | Jun 1989 |
| ProDOS BASIC 1.4 | Aug 1989 |
| ProDOS BASIC 1.4.1 | Jul 1990 |
| ProDOS BASIC 1.5 | May 1993 |
