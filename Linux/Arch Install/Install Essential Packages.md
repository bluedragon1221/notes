The base of Arch Linux is installed using the `pacstrap` command. This will take a long time depending on your internet connection, so your should probably [[Update Mirrors with Reflector|update your mirrors]].

## Update `archlinux-keyring`
Depending on how outdated your Arch Linux ISO is, you may need to force update the `archlinux-keyring` before you `pacstrap`.
```sh
pacman -Sy archlinux-keyring
```

## Run `pacstrap`
```sh
pacstrap /mnt base linux linux-firmware
```

You can add any other packages to this command. I generally add my text editor of choice, networking tools, and grub as well.
```sh
pacstrap /mnt base linux linux-firmware \
              vim zsh \
              networkmanager \
```

