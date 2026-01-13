# Installation of Alpine Linux on PC Engines APU3 (Legacy BIOS)

Guide for installing **Alpine Linux** on **PC Engines APU3** series boards **without flashing or updating the BIOS to UEFI**.  
Uses only **official published Alpine Linux images** and keeps the original factory legacy BIOS (MBR).

![PC Engines APU3 board](https://www.pcengines.ch/images/apu3.jpg)  
*Typical PC Engines APU3 – AMD GX-412TC, 3× GbE, mSATA, fanless, serial console only.*

## Project Goal

This installation guide deliberately **avoids the upgrade to a UEFI BIOS** to keep things simple, reversible, and close to factory state:

- Boot official Alpine extended ISO via legacy BIOS.
- Install to mSATA/SSD in **sys** mode 
- Minimal, secure, lightweight setup ideal for routers, firewalls, servers, or homelabs.

Tested with recent Alpine version (3.23 as of 2026).

## Main Manual

The complete step-by-step installation guide is here:

→ [**Installation of Alpine Linux on PC Engines APU3 (Legacy BIOS).md**](./docs/Installation%20of%20Alpine%20Linux%20on%20PC%20Engines%20APU3%20(Legacy%20BIOS).md)

It covers:

- Hardware prerequisites & serial console setup
- Preparing & booting the USB installer
- Boot parameters (console=ttyS0,115200)
- `setup-alpine` walkthrough
- Partitioning, networking, SSH, persistence
- Troubleshooting common issues (boot hangs, network detection, etc.)

## Quick Start (Summary)

1. Download **alpine-extended-x.y.z-x86_64.iso** from https://alpinelinux.org/downloads/
2. Write it to USB (dd, balenaEtcher, etc.)
3. Connect serial console: 115200 8N1 (use minicom/cu/PuTTY)
4. Power on APU3 → boot from USB (Esc/F10 for boot menu if needed)
5. Quickly interrupt default boot (slash)
5. At boot prompt add boot-parameters a.o. `console=ttyS0,115200`
6. Login: root (no password)
7. Run `setup-alpine` → choose **sys** install mode for full disk usage
8. Reboot → login via serial or SSH

Full details & exact commands → see the linked manual above.

## Requirements

- PC Engines APU3 board
- USB stick (≥1 GB)
- USB-to-serial adapter + cable (DB9)
- Terminal emulator on host PC
- Official Alpine extended ISO (x86_64)

## License

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](http://creativecommons.org/publicdomain/zero/1.0/)  
To the extent possible under law, the author has dedicated all copyright and related rights to this work to the **public domain** via **CC0 1.0 Universal**.  
See the [LICENSE](./LICENSE) file for details.

Use, modify, share freely — no attribution required.

## Contributing

Spotted a typo, better screenshot, missing step, or APU3-specific tip (e.g., COM2 usage, mPCIe WiFi config)?  
Issues and pull requests welcome!

Maintained by Huub – Leiden, NL (2026)  
Questions? Open an issue.

Happy routing & hacking! 🚀