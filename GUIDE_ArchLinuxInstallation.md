# Arch Linux Installation Guide

> Nuno Penim, 2021

## Disclaimer

I am not responsible for any data loss that might occur. This guide will tell you how to install Arch Linux as **the single OS in your machine**. Back up your stuff, and don't use this guide if you are looking for a dual boot.

You can use this guide freely as a base for a guide of your own to do other setup options (such as a Dual Boot version), if you give me proper credits.

Thank you.

## Preface

Congratulations, if you are reading this, you found a very special document I wrote not only to help myself, but to help other like minded individuals.

After this, you should have a full working Arch Linux installation, running the Gnome DE and with the GDM. If this is not what you are here for, Google how to do your specific Setup. I will not answer any pull requests.

This was done specifically in a 2020 Asus VivoBook X521IA (M533IA), with an AMD Ryzen 7 4700U, 16GiB of RAM and a 1TB NVMe SSD. It should work on any other machine though, with UEFI support. If you are looking for a BIOS/CSM version, you can check out my [older guide](https://github.com/nunopenim/nunopenim/blob/main/GUIDE_ArchLinuxInstallationBIOS-CSM.md)

Every problem you run into can be solved either by looking in the [Arch Wiki](https://wiki.archlinux.org/), or by searching in the [Forum](https://bbs.archlinux.org/). Currently, everything in my machine works fine, except Suspending. This is due to AMD's new s2idle not being yet supported fully by the stable version of the Linux Kernel (although, soon enough it will be), and this laptop not having deep sleep. External peripherals, such as my Wi-Fi printer work fine, however!

## Installation instructions and Steps

### Step 0: Backup your data

Or prepare it to be migrated. We will format your HDD/SDD entirely, so... yeah

### Step 1: Download the Arch Linux ISO file

The title pretty much explains it. To do it, access [https://archlinux.org/download/](https://archlinux.org/download/) and download the most recent ISO file. I recommend using BitTorrent, but it's up to you.

### Step 2: Make a bootable flash drive.

I recommend using [Etcher](https://www.balena.io/etcher/) for this step. Download the suitable version for you (Windows, macOS or Linux). The Linux version is usually an AppImage file, which runs (or should run) on all distros. If you are geeky, you could use dd on Linux, works too.

Always keep this flash drive around after, maybe label it. It will be important as it might be the only way you can solve any possible boot issues in the future.

### Step 3: Configure your machine

Disable Secure Boot! That's all I have to say. If you use a SATA SSD, set SATA mode to AHCI.

As for peripherials such as the Web Camera, I recommend activating everything. It is not a requirement, but it is needed to make sure everything works correctly. After the installation, you can disable the ones you don't use again.

**Note for SATA SSD users:** You probably still can use this guide, but more in the front, when I mention something like ```/dev/nvme0n1```, in your case it should be ```/dev/sda``` or similar. The guide is made for NVMe M.2 drives, because this is the future.

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

We will use the ```fdisk``` utility for this. List all the disks, by using ```fdisk -l```. Currently, it should appear both the USB Drive and the internal drive(s). Keep in mind the name of the proper one. In my case it was ```/dev/nvme0n1``` (as I mentioned earlier, it can also be ```/dev/sda``` or similar if you are using a NVMe SSD).

Run fdisk with the destination drive. In my case it was ```/dev/nvme0n1```, so I executed as ```fdisk /dev/nvme0n1```. Type ```d``` and press Enter/Return until all the partitions are deleted.

With the disk fully wiped now, type ```g``` and press Enter/Return. This will create a new GPT partition table. Create a **new primary partition** by typing ```n``` and pressing Enter/Return. Make it 500MB. After creating, type ```t``` and press Enter/Return. This will change the partition type to EFI System, instead of Linux.

Select the needed size (I recommend using the whole HDD, in the future I will add a specific SETUP with a Swap partition). After this, you should have your drive with a single partition, where you will install the operating system. 

Type ```w``` to write all the changes to the disk, and exit fdisk.

Assuming you are back in the Arch terminal, format the new partitions. The first one should be FAT32: do it using ```mkfs.fat -F32 /dev/nvme0n1p1```. The second one should be EXT4. You can do this using the command ```mkfs.ext4 /dev/nvme0n1p2```.

Now you are ready to install Arch Linux!

### Step 7: Install Arch Linux (Finally, eh?)

If you reached this far, you have a very strong willpower. I admire you, and I won't stop you.

Anyway, type ```pacman -Syy```. This will sync the PacMan repositories. Similar to how Debian and Debian-based distros syncronize package lists with ```apt-get update```.

After PacMan did it's thing, mount your newly made partition (I will assume it's ```/dev/nvme0n1p2```, from the previous step). You can do this by typing ```mount /dev/nvme0n1p2 /mnt```.

We are ready! Type ```pacstrap /mnt base base-devel linux linux-firmware nano```. This will install Arch Linux, the Linux Kernel and Firmware, some extra libraries for developers, nano, because... you will need a text editor. You can use vim instead of nano if you want, but having a CLI text editor is an important tool and requirement.

This step will take a while depending on your Internet connection.

### Step 8: Configuring the System

#### Step 8a: File system configurations

After installing the packages I specified previously, you will need to configure them properly, otherwise your system will not boot. Start by generating a [fstab file](https://wiki.archlinux.org/index.php/fstab). This can be done using the command ```genfstab -U /mnt >> /mnt/etc/fstab```. 

Good, now you have the correct File System parameters for Arch to boot.

#### Step 8b: System configurations

Now we will configure the System itself. Start by chrooting into it, by doing ```arch-chroot /mnt```. This is your system now, it no longer is the Live CD/USB. Your keyboard *might* be in the default settings, if such, refer to Step 5a again! Networking should still work fine, though.

We will start by configuring the Timezone. Run the ```timedatectl list-timezones``` command to list all available timezones. In my case, I will use ```Europe/Lisbon```. After you find the correct timezone for you, you can set it by running the command ```timedatectl set-timezone <TZ>```. In my case: ```timedatectl set-timezone Europe/Lisbon```.

The next step will be setting the default Language/Locale. Run ```nano /etc/locale.gen``` to open the ```/etc/locale.gen``` file (or use the text editor you installed in Step 7). In this file, uncomment your prefered Language and Locale settings (delete the # in front), in my case, I selected ```en_US.UTF-8```. Save and quit the editor. In Nano this is done with **CTRL+O**, Enter/Return, and **CTRL+X**

After doing this, regenerate the locales with the command ```locale-gen```. Add your preferred locale to the locale.conf file, with the echo command, for example in my case ```echo LANG=en_US.UTF-8 > /etc/locale.conf```, and export it to bash (so that we don't have to reboot the machine, it's really a bad time to do such now), with the command ```export LANG=en_US.UTF-8```.

These settings can be changed later on, so don't worry if you think they are not the correct ones. It's better to have *something* than *nothing*.

#### Step 8c: Network configurations

Every computer needs a hostname, to be identified in a network. With nano, create and edit the ```/etc/hostname``` file. Do this with ```nano /etc/hostname```. Inside write the hostname you want, in my case it was ```nuno-arch```. Save and exit with the **CTRL+O**, Enter/Return and **CTRL+X** trick.

You will also need a hosts file. Create it with the command ```touch /etc/hosts```. Open it with nano, in a similar fashion of the previous file. Bellow it's my hosts configuration. I recommend you to use a similar one, but replacing ```nuno-arch``` with the hostname you chose previously!

```
127.0.0.1	localhost
::1		localhost
127.0.1.1	nuno-arch #replace this!
```

Networkwise, you should be done!

#### Step 8d: Root account password

This is a simple, yet important step, due to security. To do such, type ```passwd``` and use a memorable password. That's it.

#### Step 8e: Bootloader configuration

We will use the GRUB bootloader. A quick reminder, **this guide is only for UEFI mode**.

You can start by installing the GRUB bootloader with PacMan: ```pacman -Syu grub efibootmgr```. Next, create the directory to mount the EFI partition and mount it: ```mkdir /boot/efi && mount /dev/nvme0n1p1 /boot/efi```. After this, install GRUB to this directory: ```grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi```.

Finally, generate a boot configuration: ```grub-mkconfig -o /boot/grub/grub.cfg```

### Step 9: Creating a user account and configuring SUDO

In this step, we will create a user account. Run the command ```useradd -m <usr>``` to create a new account, replacing <usr> with the desired username (in my specific case, it is ```useradd -m nuno```, which creates a new account named "nuno"). The -m flag will also create a Home directory for you, which saves the hassle of setting it up after.

After creating the user, set up a password for this new user, using the ```passwd``` command. For example, with my account, it would be ```passwd nuno```. It is similar to step 8d.

Now that you have a new user, we will configure SUDO, since you shouldn't be using the root account at all. Start by installing it with pacman: ```pacman -Syu sudo```.

SUDO comes with a special editor called ```visudo```, to allow us to edit it's configurations without damaging sudo. However visudo needs to be configured to use a CLI text editor. In our case, as with the rest of this guide, we will use ```nano``` as our text editor. To use nano as the visudo editor, run the command ```EDITOR=nano visudo```.

To add your newly created user to the sudoers, all you need to do is locate the line that says ```root ALL=(ALL) ALL``` and bellow add the line ```<usr> ALL=(ALL) ALL```, where <usr> is the username you created. As an example, in my case it would be ```nuno ALL=(ALL) ALL```. Save and exit, using the **CTRL+O**, Enter/Return and **CTRL+X** trick, that you should know by now.

Congratulations, you now have a sudo user.

### Step 10: Installing the X environment and Gnome

Almost there, now all that is missing is the DE and WM. Firstly, install the X enviroment. You can do it running the command ```pacman -Syu xorg```.

Now install Gnome, with the command ```pacman -Syu gnome```. The Gnome package contains gdm, so you don't have to worry about it.

After it finishes installing, create symlinks for the service to start up automatically. This can be done with the command ```systemctl enable gdm.service```.

I also recommend enabling NetworkManager, since it might be disabled by default, otherwise you will not have networking. This can be done with the command ```systemctl enable NetworkManager.service```.

### Step 11: Finishing up

Everything should be done now. Exit the chroot by typing ```exit```. Shutdown the machine, with the command ```shutdown now```. Remove the USB drive.

### Step 12: The first boot

There are a few tweaks you might need to do. Start up your machine and boot. If everything worked fine, you are now greeted by the GDM login window. Login

Try running the terminal, from the activities menu. If it works, you are good to go! Have fun.

If it fails to launch however, don't panic. I know Linux OSes need their terminal, but don't panic. Go to ```Gnome Settings > Region & Language```. It probably is saying some of the configurations are invalid (remember Step 8b?). Tweak these to suit your needs and reboot. Now the terminal should open. Have fun!

## Final notes

With this guide, it should have been possible for you to have a running Arch Linux installation. Any smaller problems can be solved with help from the Wiki or the Foruns. You can also search in Google, you will likely find your issue and how to solve it.

Remember to have fun. Don't bloat Arch too much, otherwise it loses it's purposes (Yeah you can start with the yabayabayaba Gnome is a RAM hog, but I don't really care, I like it). Cheers everyone.

## Troubleshooting

- You can boot to a black screen with a blinking cursor. However you can do Alt+F2 and back to Alt+F1, and Gnome/GDM should now boot fine. This is an issue related to X.org and the ```amdgpu``` driver (probably can happen with nvidia too) not loading on the correct time. Look it up on Arch Wiki, or even Google. This issue is everywhere and you shouldn't have an hard time figuring it :) 

- AMD's s2idle does not work well. To get around this issue, and because this laptop does not have deep sleep, I use hibernate or shutdown instead. This laptop contains a NVMe drive, which is significantly faster, so this is less of an hassle. I recommend setting up a swap partition, in order to hibernation to work correctly. You can also use a swap file, but I discourage it.

## Special Thanks

 - [Arch Linux](https://archlinux.org/), for their amazing, lightweight and flexible Linux distribution.
 - [Arch Linux Wiki](https://wiki.archlinux.org/), for helping me figure out what I needed and how it worked.
 - Some Threads in the [Arch Linux Forum](https://bbs.archlinux.org/), for helping me solving some issues, like how to get my printer working.
 - [balenaEtcher](https://www.balena.io/etcher/), for their amazing flashing software.
