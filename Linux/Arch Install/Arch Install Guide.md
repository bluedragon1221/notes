---
type: tutorial
description: The best Arch installation tutorial
---
## Connect to WiFi
This step is unnecessary to Ethernet users.

Connect to WiFi using `iwctl`.
```sh
iwctl station wlan0 connect [SSID]
```

## Disk Setup
Before doing this, make sure your system uses UEFI. You can check by finding the `efivars` folder.
```sh
[[ -d /sys/firmware/efi/efivars ]] && echo "UEFI" || echo "BIOS"
```
If it returns `BIOS`, refer to [[Bios Partitioning|this guide]] for partitioning and formatting.

This part is from the NixOS Manual on Partitioning and Formatting. [link](https://nixos.org/manual/nixos/stable/index.html#sec-installation-partitioning-formatting).

### Partitioning
Now we will make the actual partitions. If you are uncomfortable with using `parted`, you can use `cfdisk` in the same way.

#### Find the Right Drive
Sometimes `/dev/sda` is not the drive you want to install to. On virtual machines it can be `/dev/vda`, and on newer systems it can be `/dev/nvme0n1`. To make sure you use the right drive, check with `lsblk`.

Create a new GPT partition table (this will wipe your drive).
```sh
parted /dev/sda -- mklabel gpt
```

Add the root partition. This will fill the whole disk, besides the end part (swap space), and the 512MB in front (boot partition).
```sh
parted /dev/sda -- mkpart primary 512MB -8GB
```

Next, add a swap partition. The size required will vary according to needs. Here, we create an 8GB swap.
```sh
parted /dev/sda -- mkpart primary linux-swap -8GB 100%
```

Finally, the boot partition. It uses the reserved 512MB at the start of the disk.
```sh
parted /dev/sda -- mkpart ESP fat32 1MB 512MB
```

### Formatting
I recommend that you assign a unique symbolic label to each partition. This makes it easier to reference each partition later on.

For creating ext4 partitions, use `mkfs.ext4`. `-L` labels the partition.
```sh
mkfs.ext4 -L root /dev/sda1
```

For creating swap partitions, use `mkswap`. Assign the label with `-L`.
```sh
mkswap -L swap /dev/sda2
```

For creating boot partitions: `mkfs.fat`. Assign a label to the boot partition with `-n`. 
```sh
mkfs.fat -F 32 -n boot /dev/sda3
```

## Mount the File System
Mount the root partition to `/mnt` using the label we made earlier.
```sh
mount /dev/disk/by-label/root /mnt
```

Mount the EFI Boot partition to `/mnt/boot/efi`
You can either create this directory using `mkdir`, or use the `--mkdir` flag like so:
```sh
mount --mkdir /dev/disk/by-label/boot /mnt/boot/efi
```

Enable the swap partition.
```sh
swapon /dev/disk/by-label/swap
```

## Installation
This is the fun part. Were almost done!

### Mirrors
I use reflector to update mirrors before installing the system. [link](https://wiki.archlinux.org/title/Reflector)

Make a backup of the `mirrorlist` file before doing this, as it will overwrite the current one.
```sh
cp /etc/pacman.d/mirrorlist /ect/pacman.d/mirrorlist.bak
reflector --protocol https \
          --latest 10 \
          --sort rate \
          --save /etc/pacman.d/mirrorlist
```

### Install Essential Packages
Now we will install the base system. We do this with the `pacstrap` command. This will take a long time depending on your internet connection. (hence why we updated the mirrors)
```sh
pacstrap /mnt base linux linux-firmware
```

You can add any other packages to this command. I generally add my text editor of choice, networking tools, and grub as well.
```sh
pacstrap /mnt base linux linux-firmware \
              vim zsh \
              networkmanager \
	          efibootmgr grub \
```

#### Update `archlinux-keyring`
If you had errors in the previous step, try updating the `archlinux-keyring`.
```sh
pacman -Sy archlinux-keyring
```

Then rerun the `pacstrap` command.

## Configuration
Now we set up the system we created.

### Fstab
Generate an `fstab` file using `genfstab`. Only use `-L` if you used labels in the [[#Disk Setup]] section.

```sh
genfstab -L /mnt > /mnt/etc/fstab
```

### Chroot
Now we can enter our system using `arch-chroot`. We'll stay in the chroot for the rest of the tutorial.
```sh
arch-chroot /mnt
```

### Timezone
This sets your timezone. If you lived in Chicago, this would be your command:
```sh
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
```

Set hardware clock:
```sh
hwclock --systohc
```

### Set Locale
If this part is a bit confusing, here's a link to a video that explains it. [link](https://youtu.be/68z11VAYMS8?t=711)

Edit `/etc/locale.gen`, uncomment `en_US.UTF-8 UTF-8`.
```sh
...
#en_PH.UTF-8 UTF-8  
#en_PH ISO-8859-1  
#en_SC.UTF-8 UTF-8  
#en_SG.UTF-8 UTF-8  
#en_SG ISO-8859-1  
en_US.UTF-8 UTF-8  
#en_US ISO-8859-1  
#en_ZA.UTF-8 UTF-8  
#en_ZA ISO-8859-1  
#en_ZM UTF-8  
#en_ZW.UTF-8 UTF-8  
...
```

If you installed `vim`, you can use this `vim` command.
```vim
:%s/#en_US.UTF-8/en_US.UTF-8
:wq
```

Generate the locales by running: 
```sh
locale-gen
```

Create `/etc/locale.conf` with the following information:
```sh
### /etc/locale.conf ###

LANG=en_US.UTF-8
```

### Change hostname
Use this command to write your new hostname to `/etc/hostname`:
```sh
echo "THIS_IS_YOUR_HOSTNAME" > /etc/hostname
```

### Set Root Password
```sh
passwd
```

### Setup Grub Boot Loader
These are the commands to setup and install grub on UEFI.
```sh
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

On BIOS, 

## Create a User
Assuming you don't want to use the root account the whole time, we need to setup a user.

### Setup `sudo`
`sudo` does not come by default on our arch installation. We need to install it and change it's configuration.
```sh
pacman -S sudo
EDITOR=vim visudo
```

Uncomment the line that says `:wheel ALL=(ALL) ALL`.

### ...or `doas`
[`opendoas`](https://wiki.archlinux.org/title/Doas) is the pseudo-sudo (no pun intended) used on openbsd. Here's how you install it.
```sh
pacman -S doas
vim /etc/doas.conf
```

Allow all users in group wheel to execute commands.
```sh
### /etc/doas.conf ###

permit persist :wheel
```

### Create the User
Run this command to create a user.
```sh
useradd -mG wheel -s /bin/zsh USERNAME
```

Long-form:
```sh
useradd --create-home --gid wheel --shell /bin/zsh USERNAME
```

### Set User Password
Just like the root password.
```sh
passwd USERNAME
```

## Done
Now you can reboot. Good Luck!
