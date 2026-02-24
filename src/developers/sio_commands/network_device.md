# SIO Commands for Devices $71-$78 (N: Network)

Devices `$71` through `$78` provide the N: network device interface. These eight device IDs allow up to eight simultaneous network connections. The N: device supports a variety of protocols (TCP, UDP, HTTP, TNFS, FTP, and others), with a common set of core commands and protocol-specific extensions.

Any command not listed below is considered a **special command** and is forwarded to the underlying protocol handler. Before issuing a special command, the caller should send command `$FF` to query whether the protocol supports it.

## Core Commands

These commands are available regardless of which protocol is in use.

| Command | Description |
|---------|-------------|
| `'O'` (`$4F`) | Open - open a network connection using a devicespec URL |
| `'C'` (`$43`) | Close - close the current network connection |
| `'R'` (`$52`) | Read - read data from the network connection |
| `'W'` (`$57`) | Write - write data to the network connection |
| `'S'` (`$53`) | Status - get connection status (bytes waiting, connected, error) |
| `'E'` (`$45`) | Error - retrieve the most recent error code |
| `'T'` (`$54`) | Set translation - configure end-of-line translation mode |
| `'Z'` (`$5A`) | Set PROCEED timer rate - configure the PROCEED interrupt interval |

## File and Directory Operations

These commands operate on protocols that support filesystem semantics (e.g., TNFS, FTP, SMB).

| Command | Description |
|---------|-------------|
| `$20`   | Rename file |
| `$21`   | Delete file |
| `$25`   | Point - seek to a position in a file |
| `$26`   | Note - report the current position in a file |
| `$2A`   | Make directory |
| `$2B`   | Remove directory |
| `$2C`   | Change directory |
| `$30`   | Get current directory |

## JSON Operations

These commands provide built-in JSON parsing, allowing Atari programs to extract values from JSON responses without needing a local JSON parser.

| Command | Description |
|---------|-------------|
| `'P'` (`$50`) | Parse JSON - parse the received data as JSON |
| `'Q'` (`$51`) | Query JSON - query a value using a JSONPath expression |
| `$FB`   | Set JSON parameters - configure JSON query behavior |

## Channel Configuration

| Command | Description |
|---------|-------------|
| `'H'` (`$48`) | Set hash type - select the hash algorithm for the connection |
| `$FC`   | Set channel mode - switch between protocol and JSON modes |
| `$FD`   | Set login - set the username for authenticated protocols |
| `$FE`   | Set password - set the password for authenticated protocols |

## Protocol Query

| Command | Description |
|---------|-------------|
| `$FF`   | Query special command DSTATS - ask the protocol whether a given command is supported and what its data direction is |

## TCP-Specific Commands

These commands are available when the connection uses the TCP protocol.

| Command | Description |
|---------|-------------|
| `'A'` (`$41`) | Accept - accept an incoming connection on a listening socket |
| `'c'` (`$63`) | Close client connection - close a specific accepted client connection |

## UDP-Specific Commands

These commands are available when the connection uses the UDP protocol.

| Command | Description |
|---------|-------------|
| `'D'` (`$44`) | Set UDP destination - set the remote address and port for outgoing datagrams |

## HTTP-Specific Commands

These commands are available when the connection uses the HTTP protocol.

| Command | Description |
|---------|-------------|
| `'M'` (`$4D`) | Set channel mode - configure HTTP-specific channel behavior (e.g., collect headers, set method) |

## See Also

- [Error Codes](error_codes.md) - Error codes returned by the N: device
