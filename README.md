# Disable GPSU APEX Updates | please star the repo 🙏🏼😭

A KernelSU / Magisk module that prevents Google Play System Update (GPSU) APEX modules from staging and causing bootloops on legacy Exynos devices.

## Problem

Google's Play System Updates deliver APEX module updates silently through Google Play Services. On devices running custom ROMs beyond their official support (e.g., Galaxy S9/S9+/Note9 on One UI 7), these APEX updates can be incompatible with the device's vendor blobs and kernel, causing bootloops — especially if interrupted by a power loss.

## What This Module Does

Two layers of protection:

1. **`post-fs-data.sh`** — Runs early on every boot, before `apexd` activates. Wipes `/data/apex/sessions/*` so even if an update gets staged, it never activates.
2. **`service.sh`** — Runs after boot complete. Disables `com.google.android.modulemetadata` so Google Play stops delivering APEX updates entirely.

## Supported Devices

Tested on:
- Samsung Galaxy S9+ (G965F) — Exynos 9810, Duhan ROM One UI 7, KSU Next

Should work on any rooted Android device experiencing GPSU-related bootloops.

## Installation

### Via KernelSU / Magisk Manager
1. Download the latest release zip from [Releases](../../releases)
2. Open KernelSU Manager or Magisk Manager
3. Install the module zip
4. Reboot

### Manual (from TWRP recovery)
If you're already bootlooping:
1. Boot into TWRP recovery
2. Connect via USB
3. Run:
   ```
   adb shell rm -rf /data/apex
   adb shell mkdir -p /data/adb/modules/disable-gpsu
   adb push module.prop /data/adb/modules/disable-gpsu/
   adb push post-fs-data.sh /data/adb/modules/disable-gpsu/
   adb push service.sh /data/adb/modules/disable-gpsu/
   adb shell chmod 755 /data/adb/modules/disable-gpsu/post-fs-data.sh
   adb shell chmod 755 /data/adb/modules/disable-gpsu/service.sh
   ```
4. Reboot: `adb reboot`

### Emergency Fix (no permanent install)
If you just need to boot once without installing the module:
```
adb shell rm -rf /data/apex
adb reboot
```
This clears the corrupted APEX cache. The system will regenerate it from the system partition. Note: without the module, GPSU will re-stage the bad update eventually.

## Uninstalling

Remove the module from KernelSU/Magisk Manager, or:
```
adb shell rm -rf /data/adb/modules/disable-gpsu
adb reboot
```

This re-enables GPSU APEX updates.

## License

MIT
