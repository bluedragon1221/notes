---
tags: tutorial
description: Create a new user in Linux
---
Assuming you don't want to use the root account the whole time, here's how you create a user in Linux.

# Setup `sudo`
`sudo` does not come by default on our arch installation. We need to install it and change it's configuration.
```sh
pacman -S sudo
```

Edit the `sudoers` file with this command,
```sh
EDITOR=vim visudo
```
then uncomment the line that says `:wheel ALL=(ALL) ALL`.

# ...or `doas`
[`opendoas`](https://wiki.archlinux.org/title/Doas) is the replacement for `sudo` used on openbsd. It's [more lightweight and more secure](https://www.youtube.com/watch?v=brXd12LstgA) than `sudo`. Here's how you install it.
```sh
pacman -S doas
```

Allow all users in wheel group to execute commands.
```sh
### /etc/doas.conf ###

permit persist :wheel
```

# Create User
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
