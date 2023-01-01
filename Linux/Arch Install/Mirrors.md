I use reflector to update mirrors before installing the system. [link](https://wiki.archlinux.org/title/Reflector)

Make a backup of the `mirrorlist` file before doing this, as it will overwrite the current one.
```sh
cp /etc/pacman.d/mirrorlist /ect/pacman.d/mirrorlist.bak
reflector --protocol https \
          --latest 10 \
          --sort rate \
          --save /etc/pacman.d/mirrorlist
```
