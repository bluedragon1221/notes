# `genfstab`
## Installing
* `genftsab` is installed by default in the Arch Linux live environment.
* On other distros, it is available [here](https://github.com/glacion/genfstab)

## Usage
Generate an `fstab` with `-U`, then the mount location. `-U` uses UUIDs to identify each mount point.
```sh
genfstab -U /mnt > /mnt/etc/fstab
```

### Labels
If your partitions are labeled, you can use `-L` to use labels instead of UUIDs. see [[Create EXT4 File System#Why Labels?|Why Labels?]] for more information

TODO: manual `fstab`