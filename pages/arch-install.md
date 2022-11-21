# My Arch Install Guide
The way I like to install Arch Linux.

## Connect to Wifi
This step is unnecessary to Ethernet users.

Connect to wifi using `iwctl`.
```
iwctl station wlan0 connect [SSID]
```

## Partition and Format the Disk
Before doing this, make sure your system uses UEFI. You can check by finding the `efivars` folder.
```
ls /sys/firmware/efi/efivars
```
If it throws an error, then you are running on BIOS mode, so this guide won't work.

This part is from the NixOS Manual on Partitioning and Formatting. [link](https://nixos.org/manual/nixos/stable/index.html#sec-installation-partitioning-formatting).

### Partitioning
Now we will make the actual partitions. If you are uncomfortable with using `parted`, you can use `cfdisk` in the same way.

Create a GPT partition table (This will wipe your drive).
```
parted /dev/sda -- mklabel gpt
```

Add the root partition. This will fill the whole disk, besides the end part (swap space), and the 512MB in front (boot partition).
```
parted /dev/sda -- mkpart primary 512MB -8GB
```

Next, add a swap partition. The size required will vary according to needs, here, we create an 8GB one.

```
parted /dev/sda -- mkpart primary linux-swap -8GB 100%
```

Finally, the boot partition. It uses the reserved 512MiB at the start of the disk.

```
parted /dev/sda -- mkpart ESP fat32 1MB 512MB
```

### Formating
I recommend that you assign a unique symbolic label to each partition. This makes it easier to reference each partition later on.

For initializing Ext4 partitions, use `mkfs.ext4`. Use `-L` to add a label to the partition.

```
mkfs.ext4 -L root /dev/sda1
```

For creating swap partitions, use `mkswap`. Assign a label to the swap partition with `-L`.
```
mkswap -L swap /dev/sda2
```

For creating boot partitions: `mkfs.fat`. Assign a label to the boot partition with `-n`. 
```
mkfs.fat -F 32 -n boot /dev/sda3
```

## Mount the File System
Mount the root partition to `/mnt` using the label we made earlier.
```
mount /dev/disk/by-label/root /mnt
```

Mount the EFI Boot partition to `/mnt/boot/efi`
You can either create this directory using `mkdir`, or use the `--mkdir` flag like so:
```
mount --mkdir /dev/disk/by-label/boot /mnt/boot/efi
```

Enable the swap partition.
```
swapon /dev/disk/by-label/swap
```

## Installation
This is the fun part. Were almost done!

### Mirrors
I use reflector to update mirrors before installing the system. [link](https://wiki.archlinux.org/title/Reflector)

Make a backup of the `mirrorlist` file before doing this, as it will overwrite the current one.
```
cp /etc/pacman.d/mirrorlist /ect/pacman.d/mirrorlist.bak
reflector --protocol https \
          --latest 10 \
          --sort rate \
          --save /etc/pacman.d/mirrorlist
```

### Install Essential Packages
Now we will install the base system. We do this with the `pacstrap` command. This will take a long time depending on your internet connection. (hence why we updated the mirrors)

```
pacstrap /mnt base linux linux-firmware
```
You can add any other packages to this command. I generally add my text editor of choice, networking tools, and grub as well.
```
pacstrap /mnt base linux linux-firmware \
                 micro xclip \
                 networkmanager \
                 efibootmgr grub
```

## Configuration
Now we set up the system we created.

### Fstab
Generate an `fstab` file using `genfstab`. The `-L` flag only works if you labeled your partitions.
```
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot
Now we can enter our system using `arch-chroot`.
```
arch-chroot /mnt
```

### Timezone
This sets your timezone. If you lived in Chicago, this would be your command:
```
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
```

Set hardware clock:
```
hwclock --systohc
```

### Set Locale
Edit `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8` Generate the locales by running: 
```
locale-gen
```

Create `/etc/locale.conf` file with the following information:
```
LANG=en_US.UTF-8
```

### Change hostname
Use this command to write your new hostname to `/etc/hostname`:
```
cat "THIS_IS_YOUR_HOSTNAME" >> /etc/hostname
```

### Root Password
Set the root password with this command:
```
passwd
```

### Setup Grub Boot Loader
Use these commands to setup and install grub:
```
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

## Done
Now you can reboot, and your left with a blank system. Good Luck!
