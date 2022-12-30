## Disk Setup
Before doing this, make sure your system uses UEFI. You can check by finding the `efivars` folder.
```sh
[[ -d /sys/firmware/efi/efivars ]] && echo "UEFI" || echo "BIOS"
```
If it returns `UEFI`, refer to [[Arch Install Guide|the main guide]] for partitioning and formatting.

This part is from the NixOS Manual on Partitioning and Formatting. [link](https://nixos.org/manual/nixos/stable/index.html#sec-installation-partitioning-formatting).

### Partitioning
Now we will make the actual partitions. If you are uncomfortable with using `parted`, you can use `cfdisk` in the same way.

#### Find the Right Drive
Sometimes `/dev/sda` is not the drive you want to install to. On virtual machines it can be `/dev/vda`, and on newer systems it can be `/dev/nvme0n1`. To make sure you use the right drive, check with `lsblk`.

Create a new MBR partition table (this will wipe your drive).
```sh
parted /dev/sda -- mklabel msdos
```

Add the root partition. This will fill the whole disk, besides the end part (swap space), and the 512MB in front (boot partition).
```sh
parted /dev/sda -- mkpart primary 1MB -8GB
```

Allow the root partition to be booted from.
```sh
parted /dev/sda -- set 1 boot on
```

Add a swap partition.
```sh
parted /dev/sda -- mkpart primary linux-swap -8GB 100%
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


