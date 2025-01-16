![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# MateBook-13-2019-OpenCore
macOS on the Huawei MateBook 13 2019 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).

> [!NOTE]
> This repo is kept alive for informational purposes only.
>
> As I don't own this laptop anymore, I won't be able to provide any help or update for it.

## Abstract
Apart from the webcam and the fingerprint sensor, everything is working perfectly like on a real MacBook Air/Pro. The battery runtime is around five to six hours, similar to the battery runtime in Windows.

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources. For those who wish to improve their hackintoshing knowledge, [5T33Z0's OC-Little-Translated](https://github.com/5T33Z0/OC-Little-Translated) repository is the most comprehensive resource I've found on the subject.

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

For macOS to be able to boot on the MateBook 13 2019, the `Secure Boot` option _**must be disabled**_ in the UEFI.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a MateBook 13 2019 with this repository's EFI folder.

## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 13.6.7 Ventura and 14.5 Sonoma |
| OpenCore         | [MOD-OC v1.0.1](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.1_08932be/OpenCore-Mod-1.0.1-RELEASE.zip) |
| SMBIOS           | MacBookPro15,2 |
| UEFI Firmware    | v1.39 |
| SSD format       | APFS file system, GPT partition table |

## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-8565U (4 x 1.8 - 4.6 GHz, Whiskey Lake) |
| iGPU             | Intel UHD Graphics 620 |
| dGPU             | NVIDIA GeForce MX150 on some models |
| Audio            | Realtek ALC 256 |
| RAM              | 2x4 GB LPDDR3 2133 MHz |
| Wifi + Bluetooth | Intel Wireless-AC 9560, Bluetooth 5.0 |
| Storage          | Western Digital SN720 NVMe PCIe 512 GB SSD |
| USB Type-C       | Supports Power Delivery and DisplayPort |
| Camera           | 1 MPix camera |
| Keyboard / Trackpad | |
| Display          | 13 inch 3:2, 2160 x 1440 IPS 260 PPI, 10-Point Capacitive |
| Battery          | 41.7 Wh |
| Accelerometers, gyroscopes, ambient light sensors | |

## What works
- [x] CPU power management
- [x] CPU SpeedStep
- [x] iGPU with full acceleration
- [x] SSD drive
- [x] Sleep/hibernate and wake
- [x] Left and right USB-C ports with hotplug
- [x] Intel WLAN with fully working Apple Messages and FaceTime on macOS Ventura
- [x] Intel Bluetooth
- [ ] Webcam (depends on the camera vendor of your model)
- [x] Internal speakers, microphone and Combojack
- [x] Keyboard with working brightness, volume and mute keys
- [x] Power Button long-press opens Sleep/Shutdown menu
- [x] Lid sleep and wake
- [x] Trackpad with native multi-touch gestures
- [x] Touchscreen (disabled with `SSDT-TPL1.aml` to save power)
- [x] Battery percentage and cycle count
- [x] Ambient light sensor
- [x] USB Type-C Power Delivery

## What needs some more work
- [ ] Fix Apple Messages and FaceTime on macOS Sonoma
- [ ] Improve battery life by configuring Active State Power Management (ASPM)

## What will probably never work
- [ ] NVIDIA GeForce MX150 dGPU (disabled with `SSDT-dGPU-Off.aml`)
- [ ] Fingerprint sensor (disabled in the UEFI)
- [ ] Webcam (depends on the camera vendor of your model)

## Enabling native HiDPI display settings in macOS
To enable native HiDPI settings in the Display Preferences of macOS, download and run the [one-key-hidpi](https://github.com/jlempen/one-key-hidpi) script, select `Enable HiDPI`, then select `Manual input resolution` and enter `2160x1440 1920x1280 1600x1066 1280x854 1080x720` in the input prompt.

## Disabling CFG Lock to improve Power Management
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Disable CFG Lock`
4. Press enter
5. Restart

To verify this worked, press Space and select the `Check CFG Lock State` tool -- if it was successful, you'll see:

> This firmware has UNLOCKED MSR 0xE2 register!

Finally, edit `config.plist` and set `Kernel -> Quirks -> AppleCpuPmCfgLock` and `Kernel -> Quirks -> AppleXcpmCfgLock` to `false` and restart.

## Increase DVMT Gfx Memory to improve iGPU performance
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Set DVMT Pre-Allocated to 64M`
4. Press enter
5. Then select `Set DVMT Total Gfx Mem to MAX`
6. Press enter
7. Restart

Finally, edit `config.plist` and delete or comment out `framebuffer-fbmem`, `framebuffer-stolenmem` and `framebuffer-patch-enable` in `Device Properties -> Add -> PciRoot(0x0)/Pci(0x2,0x0)` and restart.

## Undervolting to reduce heat and improve performance
First we need to disable the Overclocking Lock in the UEFI Firmware:
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Disable Overclocking Lock`
4. Press enter
5. Restart

The `VoltageShift.kext` undervolting tool is already included and enabled in this repository's `Kexts` folder. To be able to launch the `voltageshift` command line tool from anywhere, copy [VoltageShift from the Tools folder](https://github.com/jlempen/MateBook-13-2019-OpenCore/blob/main/Tools/VoltageShift-EFI.zip) to your `/usr/local/bin` folder by entering `sudo cp voltageshift /usr/local/bin/` in the macOS terminal after navigating to the folder where you downloaded and unzipped the `voltageshift` executable file.

Refer to the instructions found in the [VoltageShift repository](https://github.com/sicreative/VoltageShift), as well as to the excellent howto found in [zearp's repository](https://github.com/zearp/Nucintosh#undervolting).

## More information on the modified UEFI Firmware variables 
| VarName | VarOffset | VarStore | From | To |
| ---------------- | -- | -- | --------- | --------- |
| CFG Lock | 0x3E | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| Overclocking Lock | 0xDA | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| VT-d | 0x10B | 0x2 (SaSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| DVMT Pre-Allocated | 0x107 | 0x2 (SaSetup) | 0x1 (32M) | 0x2 (64M) |
| DVMT Total Gfx Mem | 0x108 | 0x2 (SaSetup) | 0x2 (256M) | 0x3 (MAX) |

To revert all changes made to the UEFI Firmware variables to their default values, enable the corresponding entries in the `config.plist` file under `Misc` -> `Tools`, restart to the OpenCore menu, press space to see the list of tools and revert the changes by launching the option you wish to revert to its default value:
- `Enable CFG Lock`
- `Enable Overclocking Lock`
- `Enable VT-d`
- `Set DVMT Pre-Allocated to 32M`
- `Set DVMT Total Gfx Mem to 256M`

Repeat for every UEFI variable you wish to revert to its default value.

***Please be aware that you need to revert any changes made to your `config.plist` file before reverting the UEFI variables to their default values, or macOS won't boot anymore!***

## Fixing broken Apple Messages and FaceTime
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.4.1/HeliPort.dmg) instead.
The latest version 2.3.0 of itlwm.kext is already included in the Kext folder and `config.plist` file.

## Related repositories
* https://github.com/yusufklncc/Huawei-Matebook-13-Hackintosh
* https://github.com/Edoardo001/Matebook-13-Hackintosh
