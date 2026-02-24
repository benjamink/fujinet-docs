# ADAM Programming Overview

Programming the FujiNet on the Coleco ADAM uses the **AdamNet** bus, the ADAM's native peripheral communication network. When the ADAM powers on, it scans AdamNet and identifies connected devices. The FujiNet responds to this scan and can emulate multiple device types simultaneously.

## Emulated Devices

The FujiNet can present itself as any of the following AdamNet devices:

| Device | Description |
|--------|-------------|
| **FujiNet** | The core FujiNet control device, providing real-time clock access and network protocol converters (HTTP, HTTPS, FTP, SMB, TELNET, JSON) |
| **Keyboard** | Standard ADAM keyboard emulation (disabled in standard builds) |
| **Printer** | ADAM printer emulation |
| **Digital Data Drive** | Emulated tape drive (DDP) for loading and saving data |
| **Floppy Drive** | Emulated floppy disk drive |
| **Modem Device** | Modem emulation for BBS and serial communication |
| **Serial Port** | Serial port emulation |
| **FujiNet CP/M** | CP/M operating system support |

## AdamNet Device IDs

Two device IDs are central to FujiNet programming on the ADAM:

| Device ID | Name | Purpose |
|-----------|------|---------|
| `$0F` | **FujiNet Control** | System-level operations: WiFi management, host/device slots, clock, configuration |
| `$09` - `$0C` | **Network (N:)** | Up to four network connections for protocol-based I/O |

## Programming Model

Programs communicate with FujiNet by writing command structures to the appropriate device ID using EOS (Elementary Operating System) calls, then reading the response. The basic pattern is:

1. Build a command structure in memory
2. Write it to the device using `eos_write_character_device()`
3. Read the response using `eos_read_character_device()`
4. Check for `ACK` (`$80`) to confirm success

### Command Structure

Most commands use a structure beginning with a command byte, followed by command-specific parameters:

```c
struct {
    unsigned char cmd;      // Command byte
    unsigned short mode;    // Mode flags (command-specific)
    unsigned char trans;    // Translation mode
    unsigned char url[256]; // URL or payload
} command;
```

> **Note:** The command format for ADAM Network devices (`$09`-`$0C`) is identical to the Atari SIO command format for the corresponding N: device operations.

## FujiNet Control Device (`$0F`)

The control device at ID `$0F` handles system-level operations. Key commands include:

| Command | Description |
|---------|-------------|
| `$D1` | Device enable status |
| `$D2` | Get time |
| `$D3` | Random number |
| `$D4` | Disable device |
| `$D5` | Enable device |
| `$D6` | Set boot mode |
| `$D7` | Mount all |
| `$D8` | Copy file |
| `$E2` | Set filename for device slot |
| `$E8` | Get adapter config |
| `$F2` | Read device slots |
| `$F4` | Read host slots |
| `$F5` | Close directory |
| `$F6` | Read directory |
| `$F7` | Open directory |
| `$F8` | Mount device image |
| `$F9` | Mount host |
| `$FA` | Get WiFi status |
| `$FB` | Set SSID and connect |
| `$FC` | Get scan result |
| `$FD` | Scan networks |
| `$FE` | Get SSID |
| `$FF` | Reset FujiNet |

### Example: Reading the Real-Time Clock

```c
#include <stdio.h>
#include <conio.h>

#define FUJI_DEV 0x0F
#define ACK      0x80

typedef struct {
    unsigned char century;
    unsigned char year;
    unsigned char month;
    unsigned char day;
    unsigned char hour;
    unsigned char minute;
    unsigned char second;
} FUJI_TIME;

typedef struct {
    unsigned char cmd;
    unsigned short mode;
    unsigned char trans;
    unsigned char url[256];
} FUJI_CMD;

int io_time(FUJI_TIME *time)
{
    FUJI_CMD oc;
    unsigned char response[1024];

    oc.cmd = 0xD2; // Get Time

    if (eos_write_character_device(FUJI_DEV,
            (unsigned char *)&oc, sizeof(oc)) != ACK)
        return 1;

    if (eos_read_character_device(FUJI_DEV,
            response, sizeof(response)) != ACK)
        return 3;

    memcpy(time, response, sizeof(FUJI_TIME));
    return 0;
}

void main(void)
{
    FUJI_TIME ft;
    clrscr();
    io_time(&ft);
    printf("Date: %02u%02u-%02u-%02u\n",
        ft.century, ft.year, ft.month, ft.day);
    printf("Time: %02u:%02u:%02u\n",
        ft.hour, ft.minute, ft.second);
}
```

## Network Device (`$09`)

The Network device supports the same command set as the Atari N: device. Commands are issued by writing a command structure to device `$09`, then reading the response.

| Command | Description |
|---------|-------------|
| `'O'` | Open a network URL |
| `'C'` | Close the connection |
| `'R'` | Read data |
| `'W'` | Write data |
| `'S'` | Get status (bytes waiting, connected state) |
| `'P'` | Parse JSON |
| `'Q'` | Query JSON element |
| `'E'` | Get last error code |
| `'H'` | Set hash type |
| `$FC` | Set channel mode (e.g., JSON) |
| `$FD` | Set login credentials |
| `$FE` | Set password |

### Example: Reading JSON Data from the Network

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <eos.h>

#define NET_DEV 0x09
#define CHANNEL_MODE_JSON 0x01
#define ACK 0x80

const char *url = "N:HTTPS://oldbytes.space/api/v1/timelines/public?limit=1";
char response[1024];

void main(void)
{
    unsigned char r;

    // Open command
    struct { unsigned char cmd; char mode; char trans; char url[256]; } OC;
    // Set Channel Mode command
    struct { unsigned char cmd; char mode; } SCM;
    // Query command
    struct { unsigned char cmd; char query[128]; } QC;

    OC.cmd = 'O';
    OC.mode = 12;     // Read/Write
    OC.trans = 3;     // CR/LF to CR
    strncpy(OC.url, url, 256);

    SCM.cmd = 0xFC;
    SCM.mode = CHANNEL_MODE_JSON;

    QC.cmd = 'Q';

    printf("JSON TEST PROGRAM\n\n");

    // Open the URL
    printf("OPENING URL:\n%s\n", url);
    if (eos_write_character_device(NET_DEV,
            (unsigned char *)&OC, sizeof(OC)) != ACK) {
        printf("ERROR OPENING.\n");
        return;
    }
    printf("OPENED.\n");

    // Switch to JSON mode
    printf("SETTING CHANNEL MODE TO JSON\n");
    if (eos_write_character_device(NET_DEV,
            (unsigned char *)&SCM, sizeof(SCM)) != ACK) {
        printf("ERROR.\n");
        return;
    }

    // Parse the JSON response
    printf("PARSING JSON.\n");
    if (eos_write_character_device(NET_DEV, "P", 1) != ACK) {
        printf("ERROR.\n");
        return;
    }

    // Query for display name
    strncpy(QC.query, "/0/account/display_name", 128);
    while (eos_write_character_device(NET_DEV,
            (unsigned char *)&QC, sizeof(QC)) < 0x80);
    while (eos_read_character_device(NET_DEV,
            response, 1024) < 0x80);
    printf("\nNAME: %s\n\n", response);

    // Query for content
    strncpy(QC.query, "/0/content", 128);
    while (eos_write_character_device(NET_DEV,
            (unsigned char *)&QC, sizeof(QC)) < 0x80);
    while (eos_read_character_device(NET_DEV,
            response, 1024) < 0x80);
    printf("\n%s\n\n", response);

    // Close connection
    printf("CLOSING.\n");
    eos_write_character_device(NET_DEV, "C", 1);
}
```

## URL Format

Network URLs follow this format:

```
N:PROTO://[HOSTNAME][:PORT]/[PATH]
```

Where:
- **PROTO** is the protocol (`HTTP`, `HTTPS`, `TCP`, `UDP`, `FTP`, `TNFS`, `SMB`, `TELNET`, etc.)
- **HOSTNAME** is the destination host
- **PORT** is the port number (optional for standard ports)
- **PATH** is the resource path (optional)

## Further Reading

- [Commands for Device $0F (FujiNet Control)](../../../reference/adam-commands-0f.md)
- [Commands for Device $09 (Network)](../../../reference/adam-commands-09.md)
