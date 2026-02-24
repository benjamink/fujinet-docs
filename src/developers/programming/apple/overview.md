# Apple II Programming Overview

All communication with the FujiNet on the Apple II platform is performed through the **SmartPort** interface. SmartPort is Apple's block-device protocol for intelligent peripherals, and the FujiNet uses it to expose multiple virtual devices -- disk images, network adapters, a printer, and the FujiNet control device itself.

## SmartPort Fundamentals

Before issuing any FujiNet commands, your program must:

1. **Find the SmartPort slot** by scanning the I/O ROM space
2. **Locate the dispatcher address** within that slot
3. **Enumerate devices** to find the one you need (FUJI, NETWORK, etc.)

### Finding the SmartPort Slot

Scan each slot's ROM space (`$C100`-`$C700`) for the SmartPort signature bytes at offsets 1, 3, 5, and 7:

| Offset | Expected Byte |
|--------|---------------|
| 1 | `$20` |
| 3 | `$00` |
| 5 | `$03` |
| 7 | `$00` |

**C implementation (cc65):**

```c
uint8_t sp_find_slot(void)
{
    uint8_t s = 0;
    for (s = 7; s-- > 0;)
    {
        uint16_t a = 0xC000 + (s * 0x100);
        if ((PEEK(a+1) == 0x20) &&
            (PEEK(a+3) == 0x00) &&
            (PEEK(a+5) == 0x03) &&
            (PEEK(a+7) == 0x00))
            return s;
    }
    return 0; // Not found
}
```

### Finding the Dispatch Address

Once the slot is found, read the byte at offset `$FF` in the slot ROM, add 3 to it, and add the result to the slot base address:

```c
uint16_t sp_dispatch_address(uint8_t slot)
{
    uint16_t a = (slot * 0x100) + 0xC000;
    uint8_t j = PEEK(a + 0xFF);
    return a + j + 3;
}
```

### Finding SmartPort Devices

With the dispatcher located, enumerate devices using STATUS command `$00` to get the device count, then iterate through each device issuing STATUS command `$03` (Get DIB) to read each device's type string.

```c
int8_t sp_find_network(void)
{
    const char net[7] = "NETWORK";
    const uint8_t net_len = sizeof(net);
    int8_t err, num, i, j;

    err = sp_status(0x00, 0x00); // Get number of devices
    if (err) return -err;

    num = sp_payload[0];
    num += 2;

    for (i = 1; i < num; i++)
    {
        err = sp_status(i, 0x03); // Get DIB
        if (sp_payload[4] == net_len)
        {
            for (j = 0; j < net_len; j++)
                if (net[j] != sp_payload[5+j])
                    return 0;
            return i;
        }
    }
    return 0; // Not found
}
```

## Issuing SmartPort Commands

The FujiNet responds to four SmartPort command types:

| Command | Code | Purpose | Parameter Count |
|---------|------|---------|-----------------|
| **STATUS** | `$00` | Request information from a device | 3 |
| **CONTROL** | `$04` | Send an imperative command to a device | 3 |
| **READ** | `$08` | Read bytes from a device into a buffer | 4 |
| **WRITE** | `$09` | Write bytes from a buffer to a device | 4 |

All commands are issued by calling the dispatcher with a command code followed by a pointer to a parameter list:

```asm
    JSR DISPATCH
    DFB #STATUSCODE      ; Command code
    DW  CMDLIST           ; Pointer to parameter list
    BCS ERROR             ; Carry set on error
```

The parameter list for STATUS and CONTROL:

```asm
CMDLIST:
    DFB #$03             ; Parameter count (3)
    DFB #DEST            ; Destination device number
    DW  BUFFER           ; Buffer address (lo/hi)
    DFB STATUSCODE       ; Status or control code
```

READ and WRITE add a two-byte buffer length:

```asm
CMDLIST:
    DFB #$04             ; Parameter count (4)
    DFB #DEST            ; Destination device number
    DW  BUFFER           ; Buffer address (lo/hi)
    DW  LEN              ; Number of bytes to transfer
```

## FujiNet Device Types

The FujiNet exposes several virtual SmartPort devices. The exact list depends on configuration, but typically includes:

| Device Name | Description |
|-------------|-------------|
| `FUJINET_DISK_0` ... `FUJINET_DISK_n` | Virtual disk drives for mounting disk images |
| `NETWORK` (or `NETWORK_0`) | First network device for protocol-based I/O |
| `NETWORK_1` ... `NETWORK_3` | Additional network devices (up to 4 total) |
| `FN_PRINTER` | Virtual printer device |
| `FN_CPM` | CP/M emulation device |
| `THE_FUJI` | FujiNet control device for system-level operations |

> **Note:** SmartPort unit numbers do not necessarily correspond to logical device numbers used in BASIC. Use device enumeration to find the correct unit for each device type.

## Applesoft BASIC Network Extensions

For Applesoft BASIC programmers, FujiNet provides ampersand (`&`) extensions that simplify network programming. These must be loaded into memory before use.

### Loading the Extensions

```basic
REM Check if extensions are already loaded, otherwise load them
10000 GOSUB 10030: IF R=1 THEN PRINT "EXTENSIONS ALREADY INSTALLED.": RETURN
10010 PRINT "LOADING EXTENSIONS..."
10020 PRINT CHR$(4);"BLOAD /FUJI.APPLE/FUJIAPPLE":CALL 16384:RETURN
```

### Available Ampersand Commands

| Command | Description |
|---------|-------------|
| `&NOPEN unit, mode, trans, url$` | Open a network URL |
| `&NCLOSE unit` | Close a network connection |
| `&NSTATUS unit, bw, connected, nerr` | Get connection status |
| `&NREAD unit, var$, len` | Read bytes into a string variable |
| `&NWRITE unit, var$, len` | Write bytes from a string variable |
| `&NPRINT unit, var$[;]` | Send a string (with optional EOL suppression) |
| `&NINPUT unit, var$` | Receive a line of text |
| `&NJSON unit, onoff` | Enable or disable JSON channel mode |
| `&NQUERY unit, query$, value$` | Query a JSON element by path |
| `&NCTRL unit, command$, payload$` | Send a control command |
| `&NACCEPT unit` | Accept an incoming TCP connection |
| `&NLIST [var$]` | List SmartPort devices (or store list in variable) |
| `&NSETEOL [eol]` | Set end-of-line character |
| `&NTIME yr, mo, dy, hr, mn, s` | Get current time from FujiNet |

### NOPEN Mode Values

| Mode | Description |
|------|-------------|
| 4 | Read only (e.g., HTTP GET) |
| 6 | Read directory listing |
| 8 | Write only (e.g., HTTP PUT) |
| 12 | Read and write (typical for TCP/UDP) |
| 13 | HTTP POST |

### NOPEN Translation Values

| Translation | Description |
|-------------|-------------|
| 0 | No translation (most common) |
| 1 | CR to CR (no change) |
| 2 | LF to CR |
| 3 | CR/LF to CR |

### Example: Reading JSON Data

```basic
20 D$ = "N:HTTP://api.open-notify.org/iss-now.json"
30 Q$ = "/MESSAGE"

100 &NOPEN 0, 12, 3, D$
130 &NJSON 0, 1
140 PRINT "QUERY "; Q$
150 &NQUERY 0, Q$, O$
160 &NINPUT 0, O$
170 PRINT "VALUE: "; O$
180 &NCLOSE 0
```

### Example: Getting the Current Time

```basic
20 &NTIME YR, MO, DY, HR, MN, S
30 PRINT "DATE: "; YR; " "; MO; " "; DY
40 PRINT "TIME: "; HR; ":"; MN; "."; S
```

## URL Format

FujiNet URLs follow this format:

```
N:PROTO://[HOSTNAME][:PORT]/[PATH]
```

Where:
- **PROTO** is the protocol (`TCP`, `UDP`, `HTTP`, `HTTPS`, `SSH`, `TNFS`, `NFS`, `SMB`, etc.)
- **HOSTNAME** is the destination host (omit for listening sockets)
- **PORT** is the port number (optional for standard ports, required for listeners)
- **PATH** is the resource path (optional)

## Further Reading

- [SmartPort Commands for the FUJI Device](../../../reference/smartport-fuji.md)
- [SmartPort Commands for the NETWORK Devices](../../../reference/smartport-network.md)
- [SmartPort Commands for the PRINTER Device](../../../reference/smartport-printer.md)
- [SmartPort Commands for the CPM Device](../../../reference/smartport-cpm.md)
- Source code examples: [fujinet-apps (Apple II netcat)](https://github.com/FujiNetWIFI/fujinet-apps/tree/master/netcat/apple2)
- Assembly examples: [fujinet-nhandler (Apple II)](https://github.com/FujiNetWIFI/fujinet-nhandler/tree/master/apple2)
