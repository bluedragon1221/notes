---
type: tutorial
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
