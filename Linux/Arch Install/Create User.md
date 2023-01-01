Assuming you don't want to use the root account the whole time, we need to setup a user.

# Setup `sudo`
`sudo` does not come by default on our arch installation. We need to install it and change it's configuration.
```sh
pacman -S sudo
EDITOR=vim visudo
```

Uncomment the line that says `:wheel ALL=(ALL) ALL`.

# ...or `doas`
[`opendoas`](https://wiki.archlinux.org/title/Doas) is the replacement for sudo used on openbsd. Here's how you install it.
```sh
pacman -S doas
```

Allow all users in group wheel to execute commands.
```sh
### /etc/doas.conf ###

permit persist :wheel
```

# Create the User
Run this command to create a user.
```sh
useradd -mG wheel -s /bin/zsh USERNAME
```

Long-form:
```sh
useradd --create-home --gid wheel --shell /bin/zsh USERNAME
```

# Set User Password
```sh
passwd USERNAME
```
