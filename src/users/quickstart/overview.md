# Quickstart Guides

Choose the quickstart guide for your platform to get up and running with FujiNet as quickly as possible.

Each guide covers the essentials: hardware overview, connecting the device, first boot, WiFi configuration, and basic usage.

## Available Platforms

| Platform | Interface | Status |
|----------|-----------|--------|
| [Atari 8-bit](./atari.md) | SIO Bus | Stable / Complete |
| [Apple II & III](./apple.md) | SmartPort / Disk II | Stable / Complete |
| [Coleco ADAM](./adam.md) | AdamNet | Stable / Complete |
| [Commodore 64](./commodore.md) | IEC Bus | Beta / In Development |
| [PC MS-DOS (RS232)](./rs232.md) | Serial RS-232 | Beta / In Development |
| [Virtual FujiNet](./virtual.md) | Emulator (no hardware needed) | Stable |

> **Tip:** If you don't have any retro hardware, you can try FujiNet right now using the [Virtual FujiNet](./virtual.md) guide with just your modern computer!

## Common First Steps

Regardless of your platform, you'll generally follow these steps:

```mermaid
flowchart LR
    A[Connect FujiNet\nto your computer] --> B[Power on\nand boot]
    B --> C[Configure\nWiFi]
    C --> D[Browse hosts\nand mount disks]
    D --> E[Boot your\nfavorite software!]
```

> **Important:** FujiNet uses the ESP32 chipset, which operates on **2.4 GHz WiFi only**. If you have a dual-band (2.4/5 GHz) router with a shared SSID, you may experience connectivity issues. Consider setting up a dedicated 2.4 GHz SSID if problems arise.

## Need Help?

- Join the [FujiNet Discord](https://discord.gg/7MfFTvD) community for real-time support
- Visit [fujinet.online](https://fujinet.online) for the latest news and downloads
- Check the [User FAQ](https://fujinet.online/user-faq/) for common questions
