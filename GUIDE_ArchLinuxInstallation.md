# Arch Linux Installation Guide

> Nuno Penim, 2020

## Disclaimer

I am not responsible for any data loss that might occur. This guide will tell you how to install Arch Linux as **the single OS in your machine**. Back up your stuff, and don't use this guide if you are looking for a dual boot.

You can use this guide freely as a base for a guide of your own to do other setup options (such as a UEFI version, or Dual Boot version), if you give me proper credits.

Thank you.

## Preface

Congratulations, if you are reading this, you found a very special document I wrote not only to help myself, but to help other like minded individuals.

After this, you should have a full working Arch Linux installation, running the Gnome DE and with the GDM. If this is not what you are here for, Google how to do your specific Setup. I will not answer any pull requests.

This was done specifically in a 2012 Toshiba Portégé Z930, with an Intel Core i7-3667U, 12GiB of RAM and 256GB of SSD. It should work on any other machine though. **I did not use UEFI!**

Every problem you run into can be solved either by looking in the [Arch Wiki](https://wiki.archlinux.org/), or by searching in the [Forum](https://bbs.archlinux.org/). Currently, everything in my machine works fine (even the Fingerprint Sensor, SD Card Reader and Bluetooth). External peripherals, such as my Wi-Fi printer work as well!

## Installation instructions and Steps

### Step 0: Backup your data

Or prepare it to be migrated. We will format your HDD/SDD entirely, so... yeah

### Step 1: Download the Arch Linux ISO file

The title pretty much explains it. To do it, access [https://archlinux.org/download/](https://archlinux.org/download/) and download the most recent ISO file. I recommend using BitTorrent, but it's up to you.

### Step 2: Make a bootable flash drive.

I recommend using [Etcher](https://www.balena.io/etcher/) for this step. Download the suitable version for you (Windows, macOS or Linux). The Linux version is usually an AppImage file, which runs (or should run) on all distros. If you are geeky, you could use dd on Linux, works too.

Always keep this flash drive around after, maybe label it. It will be important as it might be the only way you can solve any possible boot issues in the future.

### Step 3: Configure your machine

In your BIOS menu, setup the boot mode to **BIOS-CSM**, **CSM** or something similar, and not to UEFI. If it is present, **Disable Secure Boot**. If, like in my machine, a new selector pops up, to select the SATA mode, select **AHCI**, if you use a SATA SSD. You can leave it on IDE if you use a HDD, it makes no difference. If you use a PCIe M.2 SSD, look up what's the best for you (NVMe probably).

As for peripherials such as the Web Camera, I recommend activating everything. It is not a requirement, but it is needed to make sure everything works correctly. After the installation, you can disable the ones you don't use again.

**Note for PCIe M.2 SSD users:** You probably still can use this guide, but more in the front, when I mention something like ```/dev/sda```, in your case it should be ```/dev/mmc0``` or similar. I don't own a machine with this technology, so I am not sure. 

### Step 4: Booting into the Live USB environment

Fully shutdown your PC. Insert (if not yet there) the Arch Linux USB you made in Step 2. Depending on your machine, **press F12, F8 or Delete**, to get into the **Boot Settings** (not BIOS options). Select the USB drive. A Arch boot selector should pop up. **Select the option to Boot Arch Linux**. After a while a blank terminal screen should greet you. Welcome to Arch Linux

### Step 5: Preparing the environment (Some steps here are optional, depends on your needs)

#### Step 5a: The right keyboard layout

If you ever used the Linux terminal, by now you realized this blank Terminal screen is a terminal like any other, and probably did some simple commands (because you have an ADHD level like mine - that's okay, you are still beautiful) and realized the keyboard layout might not be the correct one for you (unless you are in the US).

To get to the point, the command ```ls /usr/share/kbd/keymaps/i386/**/*.map.gz``` will **show you the avaliable keyboard layouts**. It can be a never ending list, use the keyboard **Page Up** and **Page Down** keys to navigate. 

After figuring out some possible keyboard layout candidates, load one and test. You can do such via the ```loadkeys``` command. 

As an example, I need the QWERTY European Portuguese keyboard layout. I located two possible candidates: ```pt-latin1.map.gz``` and ```pt-latin9.map.gz```. I ran ```loadkeys pt-latin1``` and tested using some characters in the terminal. Since it was the correct layout for me, I advanced.

## Special Thanks

 - [Arch Linux](https://archlinux.org/), for their amazing, lightweight and flexible Linux distribution.
 - [Arch Linux Wiki](https://wiki.archlinux.org/), for helping me figure out what I needed and how it worked.
 - Some Threads in the [Arch Linux Forum](https://bbs.archlinux.org/), for helping me solving some issues, like how to get my printer working.
 - [balenaEtcher](https://www.balena.io/etcher/), for their amazing flashing software.
