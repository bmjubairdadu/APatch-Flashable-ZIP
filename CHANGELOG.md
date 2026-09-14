# Changelog

All notable changes to this project are documented here.
Versioning: `vX.Y-kpA.B.C` where `X.Y` is this installer and `A.B.C`
is the embedded KernelPatch.

## [v1.0-kp0.13.8] — 2026-09-14 — First stable release ✅

Recovery-flashable root with the official APatch 11224 manager
(`me.bmax.apatch`, KernelPatch 0.13.8). Ported from a device-verified
installer base: flash → reboot → Manager shows **Installed**.

### Added
- ✨ Recovery Installer: live `boot` backup → patch (`-s "su"`) → flash →
  daemon install (official `installApatch()` layout) → on-partition verify
  (`ROOT ACTIVE`), both A/B slots patched.
- ✨ Boot Patcher: patch a stock `boot.img` on sdcard, no partition writes
  (fastboot Plan B included).
- ✨ Uninstaller: restore stock backup or live-unpatch, remove daemon.
- ✨ `APatch-flash-report.txt` on sdcard after every install.
- ✨ Professional repo: issue/PR templates, contributing guide, security
  policy, CI-built releases with SHA-256 checksums.

### Compatibility
- Universal slot detection (cmdline → bootconfig → getprop → recovery
  fstab → partition probe) for A-only, A/B, and Virtual A/B devices.
- Universal runtime mount (slot-aware system + vendor fallback + flattened
  APEX) so `kptools` runs in TWRP, OrangeFox, and Lineage recoveries —
  including `adb sideload`.
- Boot-only targeting: the kernel always lives in `boot`; `init_boot` /
  `vendor_boot` are never flashed (per official docs).
- Auth: kernel patched with default superkey `su`; set your own strong
  SuperKey inside the Manager afterward (8–63 chars, letters + digits).
- Daemon layout matches official `installApatch()`: `apd` hub binary,
  `magiskpolicy`/`resetprop` symlinks, `busybox`/`kptools` copies,
  `su_path` + `ori.img` in `/data/adb/ap/`.
