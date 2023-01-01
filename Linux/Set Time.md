
# Set Zone Info
## Find Timezone
You can list all timezones with this command.
```sh
find /usr/share/zoneinfo/
```

## Set Timezone
This sets your timezone. This is an example for Chicago.
```sh
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
```

# Set Hardware Clock
This will synchronize your hardware clock.
```sh
hwclock --systohc
```

# Enable NTP Sync
This is completely optional, but it can fix common SSL handshake errors.

```sh
timedatectl status
```
Check the line that says `System clock synchronized: `. If it says no, then you should continue.

## Installation
Install `ntp`.

## Set NTP
```sh
timdatectl set-ntp true
```