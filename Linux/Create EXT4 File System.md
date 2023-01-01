Before doing this, make sure your system uses EFI. You can check by finding the `efivars` folder.
```sh
[[ -d /sys/firmware/efi/efivars ]] && echo "EFI" || echo "BIOS"
```
If it returns `BIOS`, avoid the `EFI` sections of this guide.

## Find the Right Drive
Sometimes `/dev/sda` is not the drive you want to install to. On virtual machines it can be `/dev/vda`, and on newer systems it can be `/dev/nvme0n1`. To make sure you use the right drive, check with `lsblk`.

# Partitioning
Now we will make the actual partitions. If you are uncomfortable with using `parted`, you can use `cfdisk` in the same way.

### EFI
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

## BIOS
Create a new MBR partition table (this will wipe your drive).
```sh
parted /dev/sda -- mklabel msdos
```

Add the root partition. This will fill the whole disk, besides the end part (swap space), and the 512MB in front (boot partition).
```sh
parted /dev/sda -- mkpart primary 512MB -8GB
```

Allow the root partition to boot.
```sh
parted /dev/sda -- set 1 boot on
```

Next, add a swap partition. The size required will vary according to needs. Here, we create an 8GB swap.
```sh
parted /dev/sda -- mkpart primary linux-swap -8GB 100%
```

# Formatting

For creating ext4 partitions, use `mkfs.ext4`. `-L` labels the partition.
```sh
mkfs.ext4 -L root /dev/sda1
```

For creating swap partitions, use `mkswap`. Assign the label with `-L`.
```sh
mkswap -L swap /dev/sda2
```

## EFI
For creating boot partitions, use `mkfs.fat`. Assign a label to the boot partition with `-n`. 
```sh
mkfs.fat -F 32 -n boot /dev/sda3
```

## Why Labels?
I recommend assigning a unique symbolic label to each partition. This makes it easier to reference each partition when mounting and in other situations.

# Mounting
Mount the root partition to `/mnt` using the label we made earlier.
```sh
mount /dev/disk/by-label/root /mnt
```

Enable the swap partition.
```sh
swapon /dev/disk/by-label/swap
```

## EFI
Mount the EFI Boot partition to `/mnt/boot/efi`. You can either create this directory using `mkdir`, or use the `--mkdir` flag like so:
```sh
mount --mkdir /dev/disk/by-label/boot /mnt/boot/efi
```
