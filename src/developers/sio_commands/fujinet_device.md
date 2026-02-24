# SIO Commands for Device $70 (FujiNet Control)

Device `$70` is the FujiNet control device. It provides commands for managing the FujiNet hardware itself, including WiFi configuration, host and device slot management, directory operations, cryptographic functions, and system diagnostics.

## Diagnostics

| Command | Description |
|---------|-------------|
| `$00`   | Test |
| `$B0`   | Alter SIO timing for T0 |
| `$B1`   | Alter SIO timing for T1 |
| `$B2`   | Alter SIO timing for T2 |
| `$B3`   | Alter SIO timing for T3 |
| `$B4`   | Alter SIO timing for T4 |
| `$B5`   | Alter SIO timing for T5 |

## Status

| Command | Description |
|---------|-------------|
| `$3F`   | Get HSIO index |
| `$53`   | FujiNet command status |
| `$D1`   | Device enable status |

## Crypto and Hash

| Command | Description |
|---------|-------------|
| `$C5`   | Hash output - retrieve computed hash result |
| `$C6`   | Hash length - get length of computed hash |
| `$C7`   | Hash compute - perform hash computation on buffered input |
| `$C8`   | Hash input - send data to be hashed |

## Base64 Decode

| Command | Description |
|---------|-------------|
| `$C9`   | Base64 decode output - retrieve decoded result |
| `$CA`   | Base64 decode length - get length of decoded data |
| `$CB`   | Base64 decode compute - perform decode on buffered input |
| `$CC`   | Base64 decode input - send Base64-encoded data to decode |

## Base64 Encode

| Command | Description |
|---------|-------------|
| `$CD`   | Base64 encode output - retrieve encoded result |
| `$CE`   | Base64 encode length - get length of encoded data |
| `$CF`   | Base64 encode compute - perform encode on buffered input |
| `$D0`   | Base64 encode input - send raw data to encode |

## QR Code

| Command | Description |
|---------|-------------|
| `$BC`   | QR input - send data to encode as QR |
| `$BD`   | QR encode - perform QR code generation |
| `$BE`   | QR length - get length of QR code output |
| `$BF`   | QR output - retrieve generated QR code data |

## GUID

| Command | Description |
|---------|-------------|
| `$BB`   | Generate GUID |

## Device Management

| Command | Description |
|---------|-------------|
| `$D4`   | Disable device |
| `$D5`   | Enable device |
| `$D6`   | Set boot mode |
| `$D7`   | Mount all - mount all configured device slots |
| `$D8`   | Copy file |
| `$D9`   | Enable/disable CONFIG in D1: |
| `$DF`   | Set external SIO clock |
| `$E8`   | Get adapter config |
| `$EB`   | Set UART baud rate |
| `$F0`   | Enable UDPStream mode |
| `$FF`   | Reset FujiNet |

## Time and Random

| Command | Description |
|---------|-------------|
| `$D2`   | Get time |
| `$D3`   | Random number |

## Host Slots

Host slots define the remote servers or storage locations that FujiNet can connect to (e.g., TNFS servers, SD card).

| Command | Description |
|---------|-------------|
| `$E0`   | Get host prefix |
| `$E1`   | Set host prefix |
| `$E6`   | Unmount host |
| `$F3`   | Write host slots - save host slot configuration |
| `$F4`   | Read host slots - retrieve host slot configuration |
| `$F9`   | Mount host |

## Device Slots

Device slots map virtual devices (e.g., D1: through D8:) to disk images on mounted hosts.

| Command | Description |
|---------|-------------|
| `$DA`   | Get device slot filename |
| `$E2`   | Set filename for device slot |
| `$E3`   | Set HSIO index |
| `$E9`   | Unmount device image |
| `$F1`   | Write device slots - save device slot configuration |
| `$F2`   | Read device slots - retrieve device slot configuration |
| `$F8`   | Mount device image |

## Directory Operations

These commands allow browsing files on a mounted host.

| Command | Description |
|---------|-------------|
| `$E4`   | Set directory position |
| `$E5`   | Get directory position |
| `$F5`   | Close directory |
| `$F6`   | Read directory - retrieve next directory entry |
| `$F7`   | Open directory |
| `$E7`   | New disk - create a new disk image |

## WiFi Configuration

| Command | Description |
|---------|-------------|
| `$EA`   | Get WiFi enabled |
| `$FA`   | Get WiFi status |
| `$FB`   | Set SSID and connect |
| `$FC`   | Get scan result |
| `$FD`   | Scan networks |
| `$FE`   | Get SSID |

## App Key Storage

App keys provide a simple key-value storage mechanism for applications to persist small amounts of configuration data on the FujiNet device.

| Command | Description |
|---------|-------------|
| `$DB`   | Close app key |
| `$DC`   | Open app key |
| `$DD`   | Read app key |
| `$DE`   | Write app key |

## Complete Command Reference

For quick lookup, here is every command in numerical order:

| Command | Description |
|---------|-------------|
| `$00`   | Test |
| `$3F`   | Get HSIO index |
| `$53`   | FujiNet command status |
| `$B0`   | Alter SIO timing for T0 |
| `$B1`   | Alter SIO timing for T1 |
| `$B2`   | Alter SIO timing for T2 |
| `$B3`   | Alter SIO timing for T3 |
| `$B4`   | Alter SIO timing for T4 |
| `$B5`   | Alter SIO timing for T5 |
| `$BB`   | Generate GUID |
| `$BC`   | QR input |
| `$BD`   | QR encode |
| `$BE`   | QR length |
| `$BF`   | QR output |
| `$C5`   | Hash output |
| `$C6`   | Hash length |
| `$C7`   | Hash compute |
| `$C8`   | Hash input |
| `$C9`   | Base64 decode output |
| `$CA`   | Base64 decode length |
| `$CB`   | Base64 decode compute |
| `$CC`   | Base64 decode input |
| `$CD`   | Base64 encode output |
| `$CE`   | Base64 encode length |
| `$CF`   | Base64 encode compute |
| `$D0`   | Base64 encode input |
| `$D1`   | Device enable status |
| `$D2`   | Get time |
| `$D3`   | Random number |
| `$D4`   | Disable device |
| `$D5`   | Enable device |
| `$D6`   | Set boot mode |
| `$D7`   | Mount all |
| `$D8`   | Copy file |
| `$D9`   | Enable/disable CONFIG in D1: |
| `$DA`   | Get device slot filename |
| `$DB`   | Close app key |
| `$DC`   | Open app key |
| `$DD`   | Read app key |
| `$DE`   | Write app key |
| `$DF`   | Set external SIO clock |
| `$E0`   | Get host prefix |
| `$E1`   | Set host prefix |
| `$E2`   | Set filename for device slot |
| `$E3`   | Set HSIO index |
| `$E4`   | Set directory position |
| `$E5`   | Get directory position |
| `$E6`   | Unmount host |
| `$E7`   | New disk |
| `$E8`   | Get adapter config |
| `$E9`   | Unmount device image |
| `$EA`   | Get WiFi enabled |
| `$EB`   | Set UART baud rate |
| `$F0`   | Enable UDPStream mode |
| `$F1`   | Write device slots |
| `$F2`   | Read device slots |
| `$F3`   | Write host slots |
| `$F4`   | Read host slots |
| `$F5`   | Close directory |
| `$F6`   | Read directory |
| `$F7`   | Open directory |
| `$F8`   | Mount device image |
| `$F9`   | Mount host |
| `$FA`   | Get WiFi status |
| `$FB`   | Set SSID and connect |
| `$FC`   | Get scan result |
| `$FD`   | Scan networks |
| `$FE`   | Get SSID |
| `$FF`   | Reset FujiNet |
