# Commodore Programming Overview

Programming the FujiNet on Commodore platforms uses the **IEC (IEEE-488 derivative) bus**, the same serial bus used by disk drives, printers, and other Commodore peripherals. The FujiNet exposes two logical device numbers on this bus: a **command channel** for system operations and a **network device** for protocol-based I/O.

## Device Numbers

| Device | Purpose |
|--------|---------|
| **15** | FujiNet command channel -- system configuration, WiFi management, host/device slot operations |
| **16** | Network device -- protocol-based network I/O (HTTP, HTTPS, TCP, UDP, FTP, TNFS, etc.) |

Standard Commodore file I/O commands (`OPEN`, `PRINT#`, `INPUT#`, `GET#`, `CLOSE`) are used to communicate with both devices.

## Command Channel (Device 15)

The command channel accepts text-based payloads to configure and query the FujiNet. The general pattern is:

```basic
OPEN 1,15,15,"COMMAND"
```

Where `COMMAND` is a text string such as `ADAPTERCONFIG`, `SCAN`, `GETSSID`, etc. Responses are read back with `INPUT#` or `GET#`.

## Adapter Configuration

### Text Variant

**Payload:** `ADAPTERCONFIG`

**Response:** Eight lines of text containing SSID, Hostname, IP, Netmask, Gateway, DNS, MAC, BSSID, and firmware version.

```basic
10 PRINT CHR$(147)
20 OPEN 1,15,15,"ADAPTERCONFIG"
30 INPUT#1,S$:PRINT;
40 PRINT "    SSID: ";S$
50 INPUT#1,H$:PRINT;
60 PRINT "HOSTNAME: ";H$
70 INPUT#1,I$:PRINT;
80 PRINT "      IP: ";I$
90 INPUT#1,N$:PRINT;
100 PRINT " NETMASK: ";N$
110 INPUT#1,G$:PRINT;
120 PRINT " GATEWAY: ";G$
130 INPUT#1,D$:PRINT;
140 PRINT "     DNS: ";D$
150 INPUT#1,M$:PRINT;
160 PRINT "     MAC: ";M$
170 INPUT#1,B$:PRINT;
180 PRINT "   BSSID: ";B$
190 INPUT#1,V$:PRINT;
200 PRINT "     VER: ";V$
210 CLOSE1
220 END
```

### RAW Binary Variant

**Payload:** `ADAPTERCONFIG:RAW`

**Response:** A fixed-size binary structure suitable for C or assembly programs:

```c
struct {
    char ssid[33];
    char hostname[64];
    unsigned char localIP[4];
    unsigned char gateway[4];
    unsigned char netmask[4];
    unsigned char dnsIP[4];
    unsigned char macAddress[6];
    unsigned char bssid[6];
    char fn_version[15];
} cfg;
```

**C Example (cc65):**

```c
#include <cbm.h>
#include <stdio.h>

typedef struct {
    char ssid[33];
    char hostname[64];
    unsigned char localIP[4];
    unsigned char gateway[4];
    unsigned char netmask[4];
    unsigned char dnsIP[4];
    unsigned char macAddress[6];
    unsigned char bssid[6];
    char fn_version[15];
} AdapterConfig;

AdapterConfig ac;

void main(void)
{
    cbm_open(15, 15, 15, "adapterconfig:raw");
    cbm_read(15, &ac, sizeof(AdapterConfig));
    cbm_close(15);

    printf("%11s %s\n", "SSID:", ac.ssid);
    printf("%11s %s\n", "HOSTNAME:", ac.hostname);
    printf("%11s %u.%u.%u.%u\n", "IP:",
        ac.localIP[0], ac.localIP[1],
        ac.localIP[2], ac.localIP[3]);
    printf("%11s %u.%u.%u.%u\n", "NETMASK:",
        ac.netmask[0], ac.netmask[1],
        ac.netmask[2], ac.netmask[3]);
    printf("%11s %u.%u.%u.%u\n", "GATEWAY:",
        ac.gateway[0], ac.gateway[1],
        ac.gateway[2], ac.gateway[3]);
    printf("%11s %u.%u.%u.%u\n", "DNS:",
        ac.dnsIP[0], ac.dnsIP[1],
        ac.dnsIP[2], ac.dnsIP[3]);
    printf("%11s %02X:%02X:%02X:%02X:%02X:%02X\n", "MAC:",
        ac.macAddress[0], ac.macAddress[1], ac.macAddress[2],
        ac.macAddress[3], ac.macAddress[4], ac.macAddress[5]);
    printf("%11s %s\n", "FNVER:", ac.fn_version);
}
```

## WiFi Operations

### Set SSID (Connect to a Network)

**Payload:** `SETSSID:<SSID>,<PASSWORD>`

> **Important:** The SSID is case sensitive. Be aware of PETSCII-to-ASCII character conversion when specifying network names.

```basic
OPEN 1,15,15,"SETSSID:MyNetwork,mypassword":CLOSE1
```

#### RAW Variant

**Payload:** `SETSSID:RAW:` followed by exactly 97 bytes of binary data:

```c
struct {
    char ssid[33];       // NULL-padded
    char password[64];   // NULL-padded
} cfg;
```

The structure is appended immediately after `SETSSID:RAW:` and must be exactly 97 characters. Both fields are NULL-padded.

### Get Current SSID

**Payload:** `GETSSID`

**Response:** A single line of text containing the currently configured SSID.

```basic
10 OPEN 1,15,15,"GETSSID"
20 INPUT#1,S$
21 CLOSE1
30 ? "SSID: ";S$
40 END
```

### Scan for Networks

**Payload:** `SCAN`

**Response:** A single number indicating the count of discovered networks.

```basic
10 OPEN 1,15,15,"SCAN"
20 INPUT#1,N
30 CLOSE1
40 PRINT "# OF NETWORKS FOUND: ";N
50 END
```

### Get Scan Result

**Payload:** `SCANRESULT:<num>` (text) or `SCANRESULT:<num>:RAW` (binary)

**Text response:** `"SSID",RSSI`

```basic
60 OPEN 1,15,15,"SCANRESULT:1"
70 INPUT#1,S$,R:REM GET SSID AND RSSI
71 CLOSE 1
80 ? "SSID: ";S$;" RSSI: ";R
```

**RAW binary structure:**

```c
struct {
    char ssid[32];       // NULL-terminated and padded
    unsigned char rssi;
} result;
```

### Get WiFi Status

**Payload:** `WIFISTATUS`

**Response:** `3` = connected, `6` = disconnected.

```basic
10 OPEN 1,15,15,"WIFISTATUS"
20 INPUT #1,S
30 CLOSE 1
40 PRINT "WIFI STATUS IS ";
50 IF S=3 THEN PRINT "CONNECTED"
60 IF S=6 THEN PRINT "DISCONNECTED"
70 END
```

## Host and Device Slot Operations

### Mount Host Slot

**Payload:** `MOUNTHOST:<0-7>`

Mount the host configured at the specified slot (0-7). This must be done before a device slot referencing that host can be used.

```basic
OPEN 1,15,15,"MOUNTHOST:1":CLOSE1:REM MOUNT SECOND HOST SLOT
```

### Mount Device Slot

**Payload:** `MOUNTDRIVE:<0-3>:<MODE>`

Mount the device at the specified slot (0-3) with the given mode (1 = read, 2 = write). Device slots 0-3 correspond to IEC devices 8-11.

```basic
OPEN 1,15,15,"MOUNTDRIVE:0":CLOSE1:REM MOUNT FIRST DEVICE SLOT
```

### Open Directory

**Payload:** `OPENDIR:<0-7>:</PATH>[~FILTER]`

Opens a directory listing for a mounted host slot. The tilde (`~`) character substitutes for the NULL separator between path and filter pattern. Use `READDIR` to retrieve entries.

```basic
OPEN1,15,15,"OPENDIR:0:/~*.D64":CLOSE1:REM LIST .D64 FILES
```

## Command Reference Summary

| Payload | Response | Description |
|---------|----------|-------------|
| `ADAPTERCONFIG` | 8 text lines | Network adapter configuration |
| `ADAPTERCONFIG:RAW` | Binary struct | Adapter config as binary data |
| `SETSSID:<ssid>,<pw>` | -- | Connect to WiFi network |
| `SETSSID:RAW:` | -- | Connect using binary struct (97 bytes) |
| `GETSSID` | SSID string | Get current SSID |
| `SCAN` | Count | Scan for WiFi networks |
| `SCANRESULT:<n>` | SSID, RSSI | Get scan result (text) |
| `SCANRESULT:<n>:RAW` | Binary struct | Get scan result (binary) |
| `WIFISTATUS` | 3 or 6 | WiFi connection status |
| `MOUNTHOST:<0-7>` | -- | Mount a host slot |
| `MOUNTDRIVE:<0-3>:<mode>` | -- | Mount a device slot |
| `OPENDIR:<0-7>:</path>` | -- | Open directory for listing |

## Text vs. RAW Variants

Many commands offer both a **text** variant and a **RAW binary** variant:

- **Text** variants return human-readable, line-delimited strings suitable for BASIC programs using `INPUT#`. Fields are separated by line breaks and can be read one at a time.

- **RAW** variants return fixed-size binary structures suitable for C, assembly, or any language that can handle structured binary data. These are more efficient and avoid parsing overhead, but require knowledge of the exact structure layout.

The RAW variant is selected by appending `:RAW` to the command payload (e.g., `ADAPTERCONFIG:RAW`, `SCANRESULT:0:RAW`, `SETSSID:RAW:`).

## Further Reading

- [FujiNet Commodore Quickstart Guide](../../../guides/commodore-quickstart.md)
