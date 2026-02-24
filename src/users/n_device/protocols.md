# Supported Protocols

The N: device supports multiple network protocols, each suited to different tasks. This page provides detailed information about each protocol, including its devicespec format, connection modes, and usage notes.

## Protocol Summary

| Protocol | Transport | Connection Modes | File System Ops | Default Port |
|----------|-----------|-----------------|-----------------|--------------|
| [TCP](#tcp) | Stream | Client, Server | No | 23 (Telnet) |
| [UDP](#udp) | Datagram | Client, Server | No | 6502 |
| [HTTP/HTTPS](#httphttps) | Stream | Client only | Yes | 80 / 443 |
| [FTP](#ftp) | Stream | Client only | Yes | 21 |
| [TNFS](#tnfs) | Datagram | Client only | Yes | 16384 |

## TCP

**TCP (Transmission Control Protocol)** provides reliable, ordered, stream-oriented communication between two hosts. The underlying TCP/IP stack handles retransmission, ordering, and error correction, making it ideal for applications where data integrity is critical.

### Use Cases

- Telnet and BBS connections
- Raw terminal sessions
- Communicating with custom TCP services

### Devicespec Format

#### Client Mode

```
N[x]:TCP://<HOST>[:PORT]/
```

| Parameter | Description |
|-----------|-------------|
| `HOST` | Hostname or IPv4 address |
| `PORT` | Port number (default: 23) |

**Examples:**

```
N:TCP://BBS.FOZZTEXX.COM:23/       Connect to a BBS on port 23
N:TCP://192.168.1.1:1234/           Connect to IP address on port 1234
N:TCP://MYSERVER:6502/              Connect to custom service
```

#### Server (Listening) Mode

```
N[x]:TCP://:<PORT>/
```

Opens a listening socket on the specified port. When a client connects, you must issue an **ACCEPT** command (XIO 41) to attach the incoming client to the N: device.

**Example:**

```
N:TCP://:6502/                      Listen for connections on port 6502
```

### Limitations

> **Important:** A maximum of **4 simultaneous TCP connections** is supported. Attempting to open additional connections will return an error.

### Connection Flow

```mermaid
flowchart TD
    A[OPEN N:TCP://host:port/] --> B{Client or Server?}
    B -->|Client| C[Connect to remote host]
    B -->|Server| D[Open listening socket]
    D --> E[Wait for incoming connection]
    E --> F["XIO 41 (ACCEPT)"]
    F --> G[Client attached to N: device]
    C --> G
    G --> H[Read / Write data]
    H --> I[CLOSE]
```

## UDP

**UDP (User Datagram Protocol)** provides connectionless, datagram-oriented communication. Unlike TCP, UDP does not guarantee delivery, ordering, or duplicate protection. However, it is lightweight and efficient, making it well suited for applications that send small, frequent updates to multiple participants.

### Use Cases

- Multiplayer games
- Real-time status broadcasting
- Lightweight messaging protocols

### Devicespec Format

#### Client Mode

```
N[x]:UDP://<HOST>[:PORT]/
```

| Parameter | Description |
|-----------|-------------|
| `HOST` | Hostname or IPv4 address |
| `PORT` | Port number (default: 6502) |

**Example:**

```
N:UDP://192.168.1.8:2000/           Send datagrams to host on port 2000
```

#### Server (Listening) Mode

```
N[x]:UDP://:<PORT>/
```

Since UDP is connectionless, the source address of the last received packet is automatically used as the destination for any outgoing data.

**Example:**

```
N:UDP://:2000/                      Listen for UDP packets on port 2000
```

### TCP vs. UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | Guaranteed delivery | Best effort |
| Ordering | Preserved | Not guaranteed |
| Connection | Connection-oriented | Connectionless |
| Overhead | Higher | Lower |
| Best for | BBS, file transfer, telnet | Games, broadcasting |

## HTTP/HTTPS

**HTTP (HyperText Transfer Protocol)** and its secure variant **HTTPS** allow the N: device to interact with web servers. FujiNet supports the common HTTP methods: **GET**, **POST**, **PUT**, and **DELETE**.

When HTTPS is specified, FujiNet negotiates a TLS (formerly SSL) encrypted connection, allowing your retro computer to communicate with secure web services.

### Use Cases

- Downloading files from web servers
- Interacting with REST APIs
- Accessing secure web services (HTTPS)

### Devicespec Format

```
N[x]:<HTTP|HTTPS>://[username:password@]<HOST>[:PORT]/<PATH>[?query=value&...]
```

| Parameter | Description |
|-----------|-------------|
| `HTTP` or `HTTPS` | Protocol (HTTPS enables TLS encryption) |
| `username:password` | Optional HTTP authentication credentials |
| `HOST` | Hostname or IPv4 address |
| `PORT` | Port number (default: 80 for HTTP, 443 for HTTPS) |
| `PATH` | Path to the resource on the server |
| `?query=value` | Optional query string parameters |

**Examples:**

```
N:HTTP://ATARI-APPS.IRATA.ONLINE/BURIEDBU.COM
N:HTTPS://API.EXAMPLE.COM/DATA?FORMAT=JSON
N:HTTP://user:pass@MYSERVER/PRIVATE/FILE.TXT
```

### Supported Operations

| HTTP Method | N: Device Operation |
|-------------|-------------------|
| GET | Read (OPEN for read, then read data) |
| POST | Write (OPEN for write, write data) |
| PUT | Write with appropriate aux2 setting |
| DELETE | Delete operation (NDEL or XIO) |

### Notes

- **HTTPS connections** incur a brief pause on open while FujiNet negotiates TLS encryption with the remote host.
- HTTP authentication is performed when credentials are included in the URL.
- Query string parameters are fully supported and passed through to the server.

## FTP

**FTP (File Transfer Protocol)** enables the N: device to browse directories and transfer files on FTP servers. FujiNet handles the full FTP session lifecycle (login, transfer, logout) for each operation.

### Use Cases

- Browsing public FTP archives
- Downloading software and files
- Uploading files to an FTP server

### Devicespec Format

```
N[x]:FTP://[username:password@]<HOST>[:PORT]/<PATH>
```

| Parameter | Description |
|-----------|-------------|
| `username:password` | Optional credentials. Anonymous login is used if omitted. |
| `HOST` | Hostname or IPv4 address of the FTP server |
| `PORT` | Control port (default: 21) |
| `PATH` | Path to the file or directory |

**Examples:**

```
N:FTP://FTP.PIGWA.NET/stuff/collections/      Browse a directory
N:FTP://FTP.PIGWA.NET/welcome.msg              Download a file
N:FTP://user:pass@MYSERVER/uploads/data.txt    Authenticated access
```

### Supported Operations

| Operation | Supported |
|-----------|-----------|
| Read files | Yes |
| Write files | Yes |
| Directory listing | Yes (NLST) |
| Delete files | Yes |
| Rename files | Yes |
| Random access (NOTE/POINT) | No |
| Lock/Unlock | No |

### Notes

- If no credentials are provided, anonymous login is performed with the password `fujinet@fujinet.online`.
- EPSV (Extended Passive Mode) commands are used to establish the data port connection.
- A CWD command is issued to navigate to the desired path before any file transfer.
- **Connections are not persistent** -- a full login/logout cycle occurs for each file operation, similar to how a web browser handles FTP.

## TNFS

**TNFS (Trivial Network File System)** allows the N: device to access files on TNFS servers. Unlike the TNFS support for the D: device (which operates on disk images at the sector level), the N: device operates on **individual files** stored on the TNFS server.

### Use Cases

- Accessing files on a home TNFS server
- Sharing files across a local network
- Storing and retrieving data on network file storage

### Devicespec Format

```
N[x]:TNFS://<HOST>[:PORT]/<PATH>
```

| Parameter | Description |
|-----------|-------------|
| `HOST` | Hostname or IPv4 address of the TNFS server |
| `PORT` | UDP port (default: 16384) |
| `PATH` | Full path to the file on the server |

**Examples:**

```
N:TNFS://HOMESERVER/GAMES/FROG.EXE         Access a specific file
N:TNFS://192.168.1.50/DATA/                 Access a directory
N:TNFS://MYSERVER:16385/FILES/DOC.TXT       Non-default port
```

### Supported Operations

| Operation | Supported |
|-----------|-----------|
| Read files | Yes |
| Write files | Yes |
| Directory listing | Yes |
| Delete files | Yes |
| Rename files | Yes |
| Random access (NOTE/POINT) | Planned |
| Lock/Unlock | No |

### Notes

- The TNFS server is **mounted, used, and then unmounted** for each operation.
- The server is always mounted from the root directory (`/`).
- Anonymous login is always used -- username and password are not currently supported.
- If a D: device already has the same TNFS server mounted, the N: device will share that mount, avoiding redundant mount/unmount cycles.

## Directories vs. Files

For protocols that support file system operations (HTTP, FTP, TNFS), it is important to distinguish between directory paths and file paths:

| Path Type | Format | Example |
|-----------|--------|---------|
| Directory | Ends with `/` | `N:FTP://SERVER/stuff/games/` |
| File | No trailing `/` | `N:FTP://SERVER/welcome.msg` |

The trailing slash tells FujiNet whether to treat the resource as a directory (for listing) or a file (for reading/writing).

## Error Codes

When a protocol operation fails, FujiNet returns an error code. Common network-related errors include:

| Error | Description |
|-------|-------------|
| 131 | Protocol is in write-only mode |
| 132 | Invalid command sent to protocol |
| 133 | No protocol attached |
| 135 | Protocol is in read-only mode |
| 138 | Connection timed out |
| 165 | Invalid devicespec |
| 170 | File not found |
| 200 | Connection refused |
| 201 | Network unreachable |
| 202 | Connection timeout |
| 203 | Network is down |
| 204 | Connection reset |
| 255 | Could not allocate buffers |

For a complete list, see the [error codes reference](../error_codes.md).

## Next Steps

- [N: Device Overview](overview.md) -- high-level introduction
- [Tools and Utilities](tools.md) -- NCD, NCOPY, and other command-line tools
- [BASIC Programming Examples](basic_usage.md) -- using protocols from BASIC
