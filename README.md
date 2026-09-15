# APatch Flashable ZIP

**Recovery-flashable root (APatch 11224 / KernelPatch 0.13.8) for ARM64 Android — flash in TWRP/OrangeFox, reboot rooted.**

> 🛡️ **v1.1 bootloop guards:** the installer refuses to re-patch a kernel
> left by another tool, refuses a misnamed "stock" backup, and never
> labels a patched kernel as stock. If it aborts with a guard message,
> restore stock first — that abort **is** the bootloop prevention.

Flash the Installer ZIP in custom recovery and get root immediately on reboot — no PC, no fastboot, no manual patching. Also included: a safe Boot Patcher (patch a stock `boot.img` without touching partitions) and an Uninstaller (restore stock kernel, remove root).

> **Keywords:** APatch recovery flashable zip, APatch TWRP install, KernelPatch root zip, APatch OrangeFox sideload, `me.bmax.apatch` manager, Magisk alternative root, root without PC, boot.img patcher, KernelPatch 0.13.8.

[![Build ZIPs](https://github.com/bmjubairdadu/APatch-Flashable-ZIP/actions/workflows/build.yml/badge.svg)](https://github.com/bmjubairdadu/APatch-Flashable-ZIP/actions/workflows/build.yml)
[![Latest release](https://img.shields.io/github/v/release/bmjubairdadu/APatch-Flashable-ZIP)](https://github.com/bmjubairdadu/APatch-Flashable-ZIP/releases/latest)
[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)

---

## Which file do I need?

| File | Use it when… |
|---|---|
| `APatch-v1.1-kp0.13.8-Recovery-Installer.zip` | You want root now: flash in recovery, reboot, done ✅ |
| `APatch-v1.1-kp0.13.8-Boot-Patcher.zip` | You prefer fastboot: patch a stock `boot.img` on sdcard, flash from PC |
| `APatch-v1.1-kp0.13.8-Uninstaller.zip` | You want to unroot: restores the stock kernel backup |
| `SHA256SUMS.txt` | Verify downloads before flashing |

All three ZIPs (plus checksums) are attached to every
[release](https://github.com/bmjubairdadu/APatch-Flashable-ZIP/releases/latest).
The official Manager APK (`me.bmax.apatch`) is bundled **inside** each ZIP —
always install that copy, never a random APK.

> ⚠️ **FolkPatch (`me.yuki.folk`) and APatch (`me.bmax.apatch`) cannot coexist.**
> Both use `/data/adb/apd`, `/data/adb/ap/`, and syscall 45. Uninstall one
> fully (Uninstaller ZIP + reboot) before flashing the other.

---

## Requirements

| Requirement | Notes |
|---|---|
| ARM64 Android device, kernel 3.18 – 6.12 | Required (`apatch.dev`) |
| Custom recovery (TWRP, OrangeFox, …) | Required for flashing ZIPs |
| `CONFIG_KALLSYMS=y` in the kernel (`=y` + `KALLSYMS_ALL=y` ideal) | Required — without it root cannot work |
| Unlocked bootloader | Only needed for the fastboot method |
| Battery ≥ 50% | Recommended |

Works on A-only, A/B, and Virtual A/B devices. APatch always patches the
**`boot`** partition — never `init_boot`/`vendor_boot` (official docs:
patching those is invalid and unsupported).

---

## Method 1 — Recovery Installer (recommended, no PC)

1. Copy `APatch-v1.1-kp0.13.8-Recovery-Installer.zip` to sdcard.
2. Boot into recovery → **Install** → select the ZIP (or `adb sideload` it).
3. The installer will:
   - Detect your current boot partition and slot automatically
   - Save a stock backup to `APatch-Backup/` (keep a copy off-device!)
   - Patch the kernel with the default superkey and install `apd`
   - Copy the Manager APK to sdcard
   - Verify the flashed partition reads back as patched (`ROOT ACTIVE`)
4. Reboot → install `APatch-Manager.apk` from sdcard.
5. Open the app → set your own SuperKey when prompted (8–63 chars,
   letters + digits — never `12345678`), **Installed / Active**.

> **How do I know the flash really worked?** The recovery log must contain
> `ROOT ACTIVE on current slot`. A copy is also saved to sdcard as
> `APatch-flash-report.txt` — attach it when asking for help.

---

## Method 2 — Boot Patcher + fastboot (advanced)

1. Get your stock `boot.img` (firmware package or `adb pull /dev/block/by-name/boot`).
2. Put it on sdcard as `APatch-stock-boot.img`.
3. Flash `APatch-v1.1-kp0.13.8-Boot-Patcher.zip` in recovery.
   - Touches **no** partition. Output: `APatch-patched-boot.img` on sdcard.
4. From PC:
   ```sh
   fastboot flash boot APatch-patched-boot.img
   # A/B devices: flash the other slot too
   fastboot flash boot_a APatch-patched-boot.img
   fastboot flash boot_b APatch-patched-boot.img
   fastboot reboot
   ```
5. Install the Manager APK → set SuperKey → verify with a root checker.

---

## Uninstall / unroot

Flash `APatch-v1.1-kp0.13.8-Uninstaller.zip` in recovery. It restores the
stock backup if found, otherwise live-unpatches the kernel, and always
removes the root daemon (`/data/adb/apd`, `/data/adb/ap/`).

---

## Troubleshooting (read before opening an issue)

| Symptom | Most likely cause → fix |
|---|---|
| No `ROOT ACTIVE` in recovery log | Flash didn't reach the partition (wrong target / write-protected). Flash the Uninstaller, reboot, flash again. |
| App shows “Not installed” but `apd` runs | Manager APK signature mismatch — uninstall it, install `APatch-Manager.apk` from the ZIP, **reboot**, open the app again. |
| `kernel requires CONFIG_KALLSYMS=y` | Your kernel can't be patched — root is impossible on this kernel. |
| `new-boot.img missing` / repack failed | Missing `gzip` in recovery or corrupt download — re-download, verify SHA-256. |
| Bootloop after flash | You flashed over another tool's patch (FolkPatch/APatch mix) or a bad backup. Restore: flash the Uninstaller ZIP, or fastboot-flash your stock `boot.img`. v1.1+ refuses these cases up front. |
| `foreign patch detected` / `not stock` abort | **Not a bug — the bootloop guard fired.** Restore stock (Uninstaller with valid backup, or fastboot stock), then flash again. Never rename a patched image as stock. |
| `su: not found` in `adb shell` | Normal — APatch has no `/system/bin/su`. Grant root per app inside the Manager (Superuser page). |

Still stuck? Open a [bug report](.github/ISSUE_TEMPLATE/bug_report.yml) with
device model, Android version, recovery name/version, the recovery-log lines,
and `APatch-flash-report.txt`.

---

## FAQ

**Is this official?**
No — a community repackaging. APatch is by
[bmax121](https://github.com/bmax121/APatch), based on KernelPatch
(also bmax121). Binaries and the manager APK come from the official upstream
release; only the installer scripts here are original work (GPL-3.0).

**Do I need to enter a superkey?**
The ZIP patches with the default key so the app works out of the box; set
your own strong SuperKey inside the Manager afterward (8–63 chars, letters +
digits). Never use weak keys like `12345678`.

**FolkPatch vs APatch?**
FolkPatch (`me.yuki.folk`) is a fork of APatch (`me.bmax.apatch`) with a
different signature and extra daemons. Pick **one** — they share
`/data/adb/apd` and cannot coexist on the same install.

**Will it trip SafetyNet / Play Integrity?**
Root inherently affects attestation — no guarantees; see [apatch.dev](https://apatch.dev/).

**Can I use it with `adb sideload`?**
Yes — Lineage-style recoveries included. Each ZIP contains exactly one script,
so sideload (`/sideload/package.zip`) dispatches to the right installer.

---

## How it works

| Component | Role |
|---|---|
| `META-INF/.../update-binary` | Recovery bootstrap: picks the script by ZIP filename, sets up BusyBox + runtime |
| `assets/InstallAP.sh` | Live partition → backup → unpack → patch (`-s "su"`) → repack → flash → daemon → verify |
| `assets/PatchOnly.sh` | Same pipeline on a stock image file; writes nothing to partitions |
| `assets/UninstallAP.sh` | Restores backup or live-unpatches; removes daemon files |
| `assets/kpimg` | KernelPatch core image (0.13.8) embedded into the patched kernel |
| `lib/arm64-v8a/libkptools.so` | KernelPatch CLI (unpack/patch/verify/repack) |
| `lib/arm64-v8a/libbusybox.so` | Unix tools for recovery shell |

`kptools` links against Android's Bionic libc, so the scripts mount the real
system partition and APEX runtime before running it, then restore the
environment afterward. The daemon layout follows the official
`installApatch()`: `apd` is the hub binary, `magiskpolicy`/`resetprop` are
symlinks to it, `busybox`/`kptools` are copies, `su_path` defaults to
`/system/bin/su`, stock image saved to `/data/adb/ap/ori.img`.

---

## Build from source

No binaries are committed. The build extracts everything from the official APK:

```sh
python tools/build.py --tag v1.1-kp0.13.8
# outputs: dist/*.zip + SHA256SUMS.txt (all git-ignored)
```

Pushing a `v*` tag runs the same build in GitHub Actions and attaches the
ZIPs to the release. See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide.

---

## Versioning

`vX.Y-kpA.B.C` — `X.Y` is this installer, `A.B.C` is the embedded KernelPatch.
Only the latest release is supported ([security policy](SECURITY.md)).

---

## Credits & license

- **APatch** by [bmax121](https://github.com/bmax121/APatch),
  based on **KernelPatch** (also bmax121).
  Binaries (`kpimg`, `kptools`, `busybox`) and the Manager APK come from the
  official upstream release.
- Installer scripts (`META-INF/…/update-binary`, `assets/*.sh`) are original
  work, licensed **GPL-3.0** (see [LICENSE](LICENSE)).
- Community project, not affiliated with bmax121.
