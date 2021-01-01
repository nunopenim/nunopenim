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

**Note for PCIe M.2 SSD users:** You probably still can use this guide, but more in the front, when I mention something like ```/dev/sda```, in your case it should be ```/dev/nvme0``` or similar. I don't own a machine with this technology, so I am not sure. 

### Step 4: Booting into the Live USB environment

Fully shutdown your PC. Insert (if not yet there) the Arch Linux USB you made in Step 2. Depending on your machine, **press F12, F8 or Delete**, to get into the **Boot Settings** (not BIOS options). Select the USB drive. A Arch boot selector should pop up. **Select the option to Boot Arch Linux**. After a while a blank terminal screen should greet you. Welcome to Arch Linux

### Step 5: Preparing the environment (Some steps here are optional, depends on your needs)

#### Step 5a: The right keyboard layout

If you ever used the Linux terminal, by now you realized this blank Terminal screen is a terminal like any other, and probably did some simple commands (because you have an ADHD level like mine - that's okay, you are still beautiful) and realized the keyboard layout might not be the correct one for you (unless you are in the US).

To get to the point, the command ```ls /usr/share/kbd/keymaps/i386/**/*.map.gz``` will **show you the avaliable keyboard layouts**. It can be a never ending list, use the keyboard **Page Up** and **Page Down** keys to navigate. After figuring out some possible keyboard layout candidates, load one and test. You can do such via the ```loadkeys``` command. 

As an example, I need the QWERTY European Portuguese keyboard layout. I located two possible candidates: ```pt-latin1.map.gz``` and ```pt-latin9.map.gz```. I ran ```loadkeys pt-latin1``` and tested using some characters in the terminal. Since it was the correct layout for me, I advanced.

#### Step 5b: Wi-Fi connection

If you don't have an Ethernet connection (although it would be good, less of an hassle to setup), you can connect to Wi-Fi, via the terminal. This is done via the ```iwctl``` utility. To open it, just type ```iwctl``` on the terminal.

Inside the IWCTL utility, type ```device list```. This will show your Wi-Fi compatible devices. In my case, mine is named ```wlan0```. 

Perform a network scan, by typing ```station wlan0 scan```. After this, to check all the available networks, use ```station wlan0 get-networks```. Make sure your network is on this list. 

If your network uses WPA or WPA2 (password, not Enterprise authentication), to connect to it, just type ```station wlan0 connect "<network name>" psk```. It then will ask you for a password, and that should be it. 

Use ```quit``` to exit the utility and perform a ping (for example ```ping 8.8.8.8```), to check if you have network access. Ping should run indefinitely, and not show errors like "Network is unreachable". To quit ping, use the **CTRL+C** combination.

For a full list of options and operations inside the IWCTL utility, type ```help```.

### Step 6: Partitioning and formatting the Disk

**Warning: possible loss of data after this. Final warning: This setup is for CSM/BIOS machines, not UEFI**

We will use the ```fdisk``` utility for this. List all the disks, by using ```fdisk -l```. Currently, it should appear both the USB Drive and the internal drive(s). Keep in mind the name of the proper one. In my case it was ```/dev/sda``` (as I mentioned earlier, it can also be ```/dev/nvme0``` or similar if you are using a NVMe SSD).

Run fdisk with the destination drive. In my case it was ```/dev/sda```, so I executed as ```fdisk /dev/sda```. Type ```d``` and press Enter/Return until all the partitions are deleted.

With the disk fully wiped now, type ```o``` and press Enter/Return. This will create a new MBR partition table. Create a **new primary partition** by typing ```n``` and pressing Enter/Return. Select the needed size (I recommend using the whole HDD, in the future I will add a specific SETUP with a Swap partition). After this, you should have your drive with a single partition, where you will install the operating system. Type ```w``` to write all the changes to the disk, and exit fdisk.

Assuming you are back in the Arch terminal, format the new partition as EXT4. You can do this using the command ```mkfs.ext4 /dev/sda1```

Now you are ready to install Arch Linux!

### Step 7: Install Arch Linux (Finally, uh?)

If you reached this far, you have a very strong willpower. I admire you, and I won't stop you.

Anyway, type ```pacman -Syy```. This will sync the PacMan repositories. Similar to how Debian and Debian-based distros syncronize package lists with ```apt-get```.

After PacMan did it's thing, mount your newly made partition (I will assume it's ```/dev/sda1```, from the previous step). You can do this by typing ```mount /dev/sda1 /mnt```.

We are ready! Type ```pacstrap /mnt base base-devel linux linux-firmware nano```. This will install Arch Linux, the Linux Kernel and Firmware, some extra libraries for developers, nano, because... you will need a text editor. You can use vim instead of nano if you want, but having a CLI text editor is an important tool and requirement.

This step will take a while depending on your Internet connection.

### Step 8: Configuring the System

#### Step 8a: Boot configurations

After installing the packages I specified previously, you will need to configure them properly, otherwise your system will not boot. Start by generating a [fstab file](https://wiki.archlinux.org/index.php/fstab). This can be done using the command ```genfstab -U /mnt >> /mnt/etc/fstab```. Good, now you have the correct File System parameters for Arch to boot.

#### Step 8b: System configurations

Now we will configure the System itself. Start by chrooting into it, by doing ```arch-chroot /mnt```. This is your system now, it no longer is the Live CD/USB. Your keyboard *might* be in the default settings, if such, refer to Step 5a again! Networking should still work fine, though.

We will start by configuring the Timezone. Run the ```timedatectl list-timezones``` command to list all available timezones. In my case, I will use ```Europe/Lisbon```. After you find the correct timezone for you, you can set it by running the command ```timedatectl set-timezone <TZ>```. In my case: ```timedatectl set-timezone Europe/Lisbon```.

The next step will be setting the default Language/Locale. Run ```nano /etc/locale.gen``` to open the ```/etc/locale.gen``` file (or use the text editor you installed in Step 7). In this file, uncomment your prefered Language and Locale settings (delete the # in front), in my case it was ```en_US.UTF-8```. Save and quit the editor. In Nano this is done with **CTRL+W**, Enter/Return, and **CTRL+X**

After doing this, regenerate the locales with the command ```locale-gen```. Add your preferred locale to the locale.conf file, with the echo command, for example in my case ```echo LANG=en_US.UTF-8 > /etc/locale.conf```, and export it to bash (so that we don't have to reboot the machine, it's really a bad time to do such now), with the command ```export LANG=en_US.UTF-8```.

These settings can be changed later on, so don't worry if you think they are not the correct ones. It's better to have *something* than *nothing*.

#### Step 8c: Network configurations



## Special Thanks

 - [Arch Linux](https://archlinux.org/), for their amazing, lightweight and flexible Linux distribution.
 - [Arch Linux Wiki](https://wiki.archlinux.org/), for helping me figure out what I needed and how it worked.
 - Some Threads in the [Arch Linux Forum](https://bbs.archlinux.org/), for helping me solving some issues, like how to get my printer working.
 - [balenaEtcher](https://www.balena.io/etcher/), for their amazing flashing software.
