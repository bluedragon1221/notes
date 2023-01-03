```sh
pacman -S efibootmgr grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```
