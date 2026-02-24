# Error Codes for N: Device

The N: network device returns the following error codes. Error code 144 indicates a critical protocol-level error; when this code is reported via SIO, the actual error can be retrieved from byte 4 of the STATUS response.

## Protocol Errors

| Code | Description |
|------|-------------|
| 131  | Protocol is in write-only mode (attempted read on a write-only connection) |
| 132  | Invalid command sent to protocol |
| 133  | No protocol attached (connection not open or URL scheme not recognized) |
| 135  | Protocol is in read-only mode (attempted write on a read-only connection) |
| 138  | Timed out |
| 144  | Critical error occurred; retrieve the actual error code from byte 4 of STATUS |
| 146  | Command not implemented by the current protocol handler |

## File and Path Errors

| Code | Description |
|------|-------------|
| 151  | File exists (target already present during a create or rename) |
| 162  | No space on device |
| 165  | Invalid devicespec (malformed URL or device specification) |
| 167  | Access denied |
| 170  | File not found (emitted by filesystem protocol adaptors) |

## Network Errors

| Code | Description |
|------|-------------|
| 200  | Connection refused (`ECONNREFUSED`) |
| 201  | Network unreachable (`ENETUNREACH`) |
| 202  | Connection timeout (`ETIMEDOUT`) |
| 203  | Network is down (`ENETDOWN`) |
| 204  | Connection reset during read/write (`ECONNRESET`) |
| 205  | Connection in progress (`EAGAIN`) |
| 206  | Address in use (`EADDRINUSE`) |
| 207  | Not connected |
| 208  | Server not running |
| 209  | No connection waiting (no pending client on a listening socket) |
| 210  | Service not available |
| 211  | Connection aborted |
| 212  | Invalid username or password |

## Memory Errors

| Code | Description |
|------|-------------|
| 255  | Could not allocate receive or transmit buffers |
