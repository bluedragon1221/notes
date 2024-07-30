---
tags: tutorial
description: Setup pipewire to enable audio
---
# Installation
Install the following packages,
```sh
sudo pacman -S pipewire wireplumber pipewire-pulse
```

enable services,
```sh
systemctl --user enable pipewire-pulse
```

And reboot.
```sh
reboot
```

Now you can use `pamixer` to control the volume:
```sh
# increase volume
pamixer -i 10

# decrease volume
pamixer -d 10

# get volume
pamixer --get-volume

# toggle mute
pamixer -t

# get mute
pamixer --get-mute
```
