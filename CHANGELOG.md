# Changelog

All notable changes to this project are documented here.
Versioning: `vX.Y-kpA.B.C` where `X.Y` is this installer and `A.B.C`
is the embedded KernelPatch.

## [v1.1-kp0.13.8] — 2026-09-15 — bootloop guards 🛡️

A kernel patched by one tool must never be re-patched or live-unpatched
by another tool's kptools/kpimg — cross-version patch metadata is
incompatible and the result does not boot. v1.0 trusted filenames and
re-patched anything; v1.1 verifies everything before touching partitions.

### Fixed
- 🛡️ **Foreign-patch refusal:** installer aborts instead of re-patching a
  kernel left by another tool/version (FolkPatch ↔ APatch mixes, custom
  keys). Detection = plaintext `superkey` + embedded kpimg `compile_time`
  must both match this ZIP; anything else gets a clear abort, not a
  bootloop. Same guard in Uninstaller live-unpatch path.
- 🛡️ **Stock-backup validation:** a saved `stock-*.img` is unpacked and
  proven `patched=false` before reuse; a misnamed patched file aborts
  instead of becoming the patch source.
- 🛡️ **Never label patched as stock:** new backups are saved only from
  proven-stock kernels; on an already-patched device the installer warns
  and keeps the old file.
- 🛡️ **PatchOnly honesty:** refuses already-patched source images with an
  explicit another-tool warning (never patch a patched image).

### Why v1.0 bootlooped
- Flashing APatch ZIP over a FolkPatch-patched kernel (different kpimg
  190KB vs 474KB, different patch metadata) re-patched incompatible
  structures → non-booting kernel. The v1.0 log even said "re-patching
  with same key" while the key/tool differed. v1.1 detects this and
  refuses before writing anything.

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
