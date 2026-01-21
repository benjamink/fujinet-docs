# Nightly Firmware Builds

Every night an [automated GitHub Action](https://github.com/FujiNetWIFI/fujinet-firmware/blob/master/.github/workflows/nightly.yml) is triggered to build the FujiNet Firmware for multiple platforms that are in a working state currently.  If successful, the resulting firmware ZIP file is attached to a permanent special GitHub Pre-Release called [Nightly Builds](https://github.com/FujiNetWIFI/fujinet-firmware/releases/tag/nightly).  

If you wish to try the bleeding edge current state of the `fujinet-firmware` code but don't have a build environment of your own then this is the place to go!  Download the appropriate file to your local computer & use the [Fujinet Flasher](https://fujinet.online/download/) tool to flash your FujiNet device with this custom-built ZIP file.  

> [!WARNING]
> There is NO guarantee that these builds work!!

These builds are **NOT** official builds & are provided 100% **AS-IS** with absolutely no guarantee that they will work.  Only use these builds if you know what you are doing & are willing to troubleshoot any problems on your own.  

Of course reporting any bugs you run across would be greatly appreciated but they should be accompanied by console logs & any other appropriate details that might help in troubleshooting the issues.  Generally the [FujiNet Discord](https://discord.gg/yKtS3fxU) is the best place to discuss any issues you find in these builds.