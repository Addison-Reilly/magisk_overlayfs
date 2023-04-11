# Magisk Overlayfs

- Make system partition (`/system`, `/vendor`, `/product`, `/system_ext`) become read-write. Important: you can only modify content in subdirectories of partitions. It's known that cover entire partitions with overlayfs will cause some problem.
- Use `/data` as upperdir for overlayfs to store modifications. All modifications to overlayfs partition will not be made directly, but will be stored in upperdir, so it is easy to revert.
- Support Magisk version 23.0+ and latest version of KernelSU

> If you are interested in OverlayFS, you can read documentation at <https://docs.kernel.org/filesystems/overlayfs.html>

> If you can't modify system files with MT File Manager, try using [Material Files](https://github.com/zhanghai/MaterialFiles) instead!

## Build

There is two way:
- Fork this repo and run github actions
- Run `bash build.sh` (On Linux/WSL)

## Change OverlayFS mode

- OverlayFS is mounted as read-only by default

- Configure overlayfs mode in `mode.sh` to change mode of OverlayFS

> Read-write mode of overlayfs will cause baseband on some devices stop working

```
# 0 - read-only but can still remount as read-write
# 1 - read-write default
# 2 - read-only locked (cannot remount as read-write)

export OVERLAY_MODE=2
```

- OverlayFS upper loop device will be setup at `/dev/block/overlayfs_loop`
- On Magisk, OverlayFS upper loop are mounted at `$(magisk --path)/overlayfs_mnt`. You can make modifications through this path to make changes to overlayfs mounted in system.

## Overlayfs-based Magisk module

- If you want to use overlayfs mount for your module, add these line to the end of `customize.sh`

```bash

OVERLAY_IMAGE_EXTRA=0     # number of kb need to be added to overlay.img
OVERLAY_IMAGE_SHRINK=true # shrink overlay.img or not?
INCLUDE_MAGIC_MOUNT=false # enable legacy Magisk mount or not when Magisk_OverlayFS is disabled

if [ -f "/data/adb/modules/magisk_overlayfs/util_functions.sh" ] && \
    /data/adb/modules/magisk_overlayfs/overlayfs_system --test; then
  ui_print "- Add support for overlayfs"
  . /data/adb/modules/magisk_overlayfs/util_functions.sh
  support_overlayfs
  rm -rf "$MODPATH"/system
fi
```

- We mounted your overlay modules at `$MAGISKTMP/overlayfs_modules` so you can modify it without having to manually mount it

## Bugreport

- Please include `/cache/overlayfs.log`

## Reset overlayfs

- Remove `/data/adb/overlay` and reinstall module

## Without Magisk

- Possible to test:

```bash
mkdir -p /data/overlayfs
./overlayfs_system /data/overlayfs
```

## Source code

- <http://github.com/HuskyDG/Magisk_OverlayFS>