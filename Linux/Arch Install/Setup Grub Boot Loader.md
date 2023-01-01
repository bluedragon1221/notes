## Setup Grub Boot Loader
These are the commands to setup and install grub on UEFI.
```sh
pacman -S efibootmgr grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```