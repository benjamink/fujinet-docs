As of Feb 2026 the current state of MSX Fujinet is that it can boot a 8,16,32k rom cartridges.  For hardware you would need to build your own prototype hardware.  What seems to be working for some is to take a blank [MSX Cartridge card](https://discord.com/channels/655893677146636301/721019729728372817/1437968026824736828) and a [pico2](https://discord.com/channels/655893677146636301/721019729728372817/1437988506860523611) and wire it together.  On the pico the [Fujiversal](https://github.com/FozzTexx/fujiversal) firmware is reported to work against a USB connected pc instead of an ESP32.  Then you can boot the [fujinet-config msx](https://github.com/FujiNetWIFI/fujinet-config/tree/msx#) rom on the MSX.

You can still work on developing software with [OpenMSX](https://github.com/FujiNetWIFI/openMSX/tree/feat/fujinet) by locally building OpenMSX.  Once built and run you can enable the Fujinet extension in a slot which will boot the fujinet-config rom.  When booting you will need port=1985 in [BIOP] section of fnconfig.ini  You will want to connect OpenMSX Fujinet to Fujinet-lwn running locally built from https://github.com/FujiNetWIFI/fujinet-firmware/tree/fujiversal  This has worked on mac and linux in Feb 2026.

The [fujinet-lib](https://github.com/FozzTexx/fujinet-lib-experimental) for MSX is experimental, but functional.

Building MSX rom requires z88dk nightly(after Nov-25-25 https://github.com/z88dk/z88dk/commit/e9f9392e846f2a4f2e715947779282b670cbf475) 

[Defoogi](https://github.com/FozzTexx/defoogi) is a docker container with all the tools to build everything Fujinet.  And a convenient wrapper script to share host directory with container so that you can just type 'defoogi make' to build from the host.  For MSX it needs a recent z88dk which looks like https://github.com/mk2s/defoogi/commit/f8dca0ad0277117cd21039cad56c7b13dec5cb9e

With this a new contributor may be able to build and port Fujinet apps like:
* https://github.com/FujiNetWIFI/fujinet-lib-examples
* https://github.com/FujiNetWIFI/fujinet-apps
* https://github.com/FujiNetWIFI/fujinet-battleship
* https://github.com/FujiNetWIFI/fujinet-iss-tracker

However since we don't have .dsk emulation everything needs to be .rom executable.

For the latest, don't forget to check the [Discord MSX channel](https://discord.com/channels/655893677146636301/931240268773093476)