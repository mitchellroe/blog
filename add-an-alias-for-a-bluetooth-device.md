# Add an alias for a Bluetooth device

## Method 1: `bt-device`

```
# bt-device --set $NAME Alias $ALIAS
# bt-device --set $MAC_ADDRESS Alias $ALIAS

# systemctl restart bluetooth
```

For example:
```
# bt-device --set 95:05:BB:22:E2:A4 Alias "Soundcore Life Q20+ (work)"
Alias: Soundcore Life Q20+ -> Soundcore Life Q20+ (work)
# systemctl restart bluetooth
```

## Method 2: `bluetoothctl`

```
# bluetoothctl
> devices
> connect $MAC_ADDRESS
> set-alias $ALIAS
```
