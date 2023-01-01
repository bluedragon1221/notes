Now we will install the base system. We do this with the `pacstrap` command. This will take a long time depending on your internet connection. (hence why we [[Mirrors|updated mirrors]])
```sh
pacstrap /mnt base linux linux-firmware
```

You can add any other packages to this command. I generally add my text editor of choice, networking tools, and grub as well.
```sh
pacstrap /mnt base linux linux-firmware \
              vim zsh \
              networkmanager \
```

### Update `archlinux-keyring`
If you had errors in the previous step, try updating the `archlinux-keyring`.
```sh
pacman -Sy archlinux-keyring
```

Then rerun the `pacstrap` command.
