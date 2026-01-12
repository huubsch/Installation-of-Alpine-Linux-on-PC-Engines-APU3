
# Installation of Alpine Linux on PC Engines APU3 (Legacy BIOS)

This document describes how to install **Alpine Linux** on a **PC Engines APU3B2** system using the legacy BIOS (MBR-based).  

---

## Prerequisites

### Hardware

- PC Engines **APU3B2** board
- **mSATA** storage module
- Boot / utility media:
  - **microSD card** (minimum 4 GB, recommended), or
  - USB flash drive (minimum 4 GB)
- **Serial cable** (RS232 to USB)

### Host System

- Linux laptop with:
  - `minicom`  
    - Serial settings: **115200 8N1**
  - `nmap`

---

## Background and Rationale

There are two common approaches to install Home Assistant or FreedomBox:

1. Running as a **Docker container** on an existing Linux system
2. Using a **prebuilt OS image** (Home Assistant OS or FreedomBox image)

The PC Engines APU uses a **legacy BIOS** (MBR-based, no UEFI).  
The Home Assistant OS image and FreedomBox image **require UEFI** and will **not boot** unless the BIOS is upgraded.

Running Alpine Linux with Docker avoids the need for a BIOS upgrade and is therefore the **simpler**.

---

## 1. Installing Alpine Linux on the APU (Legacy BIOS)

### 1.1 Preparing the Boot Media

1. Download the Alpine Linux installer from:  
   https://alpinelinux.org/downloads/

2. Select and download:  
alpine-extended-3.23.0-x86_64.iso

3. Write the ISO image to a **microSD card** (recommended) or **USB flash drive** using:
- BalenaEtcher
- `dd`
- Any equivalent imaging tool

#### Why microSD is recommended

- Reliable booting from the APU BIOS
- Can be used to install Alpine onto the mSATA disk
- Installing Alpine on mSATA **from a USB stick often fails**

#### Workaround if microSD cannot be written on the host

- Use a USB stick to boot the installer
- Insert a microSD card into the APU
- Install Alpine onto the microSD card
- Boot from microSD and then install Alpine onto the mSATA disk

---

### 1.2 Hardware Preparation

1. Install the **mSATA module** in the **left-most slot** (next to the serial connector)
2. Connect the **serial cable** between the APU and the laptop
3. Connect a network cable (internet) at the APU eth0, this is the port next to the serial connector)
3. Switch to a **text console** on the laptop (`Ctrl+Alt+F1`, back via F7). Do **not** use a graphical terminal
4. Start `minicom` as root:
  `sudo minicom -D /dev/ttyUSB0`

### 1.3 Booting the Alpine Installer
1.	Insert the prepared microSD card
2.	Power on the APU
3.	Press F10 at the BIOS prompt to select the boot device
4.	At or just before the boot: prompt, press `/` to interrupt automatic boot
5.	Enter the following boot command (only one leading slash):

`/boot/vmlinuz-lts modules=loop,squashfs,sd-mod,usb-storage nomodeset console=ttyS0,115200 initrd=/boot/initramfs-lts`

### 1.4 Installing Alpine Linux to mSATA
1.	Log in as root (no password initially)
2.	Start the installer:
        `setup-alpine`
3.	Follow the installer prompts:
	◦	Install target: mSATA (usually /dev/sda)
	◦	Select a specific mirror (do not use the random option)
	◦	Installation type: `sys`
	◦	Create a user account (recommended for SSH access)
4.	Reboot when installation finishes
	◦	Remove the microSD card
	◦	Interrupt boot again to enable serial console (same boot command)
5.	Log in as root
6.	Identify disks: 
	     `fdisk -l `
7.	Note the IP address for later SSH access:
         ` ifconfig `
8	(Optional) Enable root SSH login: edit /etc/ssh/sshd_config and add:
        `PermitRootLogin yes`
9	Reboot You should now be able to log in via SSH and disconnect the serial cable.

### 1.5 Enabling the Serial Console (Experimental)
⚠️ Note: This procedure should be verified on your system. In fact use of SSH is more versatile and convenient.

1. Edit extlinux configuration
      Edit /boot/extlinux.conf:
	  •	Add as the first line:
        `SERIAL 0 115200`
	  •	In the APPEND line, replace quiet with:
        `console=ttyS0,115200`
2. Make settings persistent; edit /etc/update-extlinux.conf:
      . `default_kernel_opts="console=ttyS0,115200"`
      .`serial_port=0`
      .`serial_baud=115200`
3. Apply changes:
      `update-extlinux`
4. Check: `cat /boot/extlinux.conf` and 
5. Reboot. 
The system should now boot with the serial console enabled by default.

### 2. Base System Configuration
2.1 Update Alpine Linux
    `apk update \
    apk upgrade \
    reboot \
    cat /etc/alpine-release`

2.2 Edit /etc/apk/repositories, uncomment the appropriate community repository, for example:

https://dl-cdn.alpinelinux.org/alpine/v3.23/community
	
Update packages:
    apk update
    apk upgrade