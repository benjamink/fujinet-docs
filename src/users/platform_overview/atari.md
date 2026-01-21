# Atari

In some cases, the devices FujiNet provides are meant to simulate real Atari peripherals, such as floppy disk drives (`D:` devices), RS232 and modem interfaces (`R:` devices), and more.

Utilizing the device's Wi-Fi networking capabilities, it's possible to connect to other devices on a local network or Internet, e.g. [Bulletin Board Systems (BBSes)](https://en.wikipedia.org/wiki/Bulletin_board_system) or other systems over Telnet, or even mounting floppy disk images from the "cloud".

## Provided Devices

| Device        | Status                | Notes                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| C:<br>(Cassette) | Prototype Working     | Load a CAS image (FUJI format) from MicroSD named test.cas. Write CAS file to MicroSD. Use browser to set PLAY or RECORD state. Short-press Button B to enable the C: device. Install a 10-kohm pulldown resistor on the MOTOR line. |
| D:<br>(Disk)     | Working               | Load floppy disk images from onboard MicroSD or networked TNFS server. Currently supports ATR and XEX. ATX in progress                                                                                                               |
| N:<br>(Network)  | Working / In Progress | NEW networking device. FujiNet configuration commands in place and working (WiFi, mounting, etc). TCP/UDP working. Handler in progress.                                                                                              |
| Other         |                       | SIO2BT Bluetooth Connection. Apetime Real Time Clock (NTP). SAM Text To Speech as a printer, voice output from #FujiNet to Atari. MIDIMaze network gaming.                                                                           |
| P:<br>(Printer)  | Working               | Printer output saved to PDF files downloadable from the device. Available Printers: 820, 822, 825, 1020, 1025, 1027, 1029, Espon 80, Okimate 10, HTML for copy/paste, GRANTIC Screen Printer.                                        |
| R:<br>(Modem)    | Working               | 850 Modem emulation, supports Type 1 Poll to load handler. Works with existing communications programs such as Ice-T, BobTerm, AMODEM, PLATOTERM, and BBS servers.                                                                   |

Since devices are handled via the Atari OS's Central I/O (CIO) subsystem, practically any programming language on the Atari will be able to make use of these network features. For example, here's a simple networked program in BASIC:

```basic
10 OPEN #1,12,0,"N:HTTP://WWW.GOOGLE.COM/"
20 DIM A$(1024):TRAP 100
30 INPUT #1,A$:PRINT A$:GOTO 30
100 CLOSE #1
```
On top of TLS and UDP, cryptographic protocols designed to provide communications security over computer networks, [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) and [Datagram Transport Layer Security (DTLS)](https://en.wikipedia.org/wiki/Datagram_Transport_Layer_Security) respectively, are also a possibility, thanks to the computing horsepower of device powering FujiNet.

## More information
The information from [the "#FujiNet - a WIP SIO Network Adapter for the Atari 8-bit" thread on the AtariAge forums](https://atariage.com/forums/topic/298720-fujinet-a-wip-sio-network-adapter-for-the-atari-8-bit/) should all be covered here in the wiki, and/or on the FujiNet website.  For now, visit that thread for more info.