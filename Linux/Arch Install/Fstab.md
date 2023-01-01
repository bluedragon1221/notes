## Fstab
Generate an `fstab` file using `genfstab`. Only use `-L` if you used labels in the [[Setup Disks]] section.

```sh
genfstab -L /mnt > /mnt/etc/fstab
```