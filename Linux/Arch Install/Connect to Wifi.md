Connect to WiFi using `iwctl`.
```sh
iwctl station wlan0 connect [SSID]
```

## Find SSID
If you don't know your network's SSID, use this command to list them.
```sh
iwctl station wlan0 get-networks
```
