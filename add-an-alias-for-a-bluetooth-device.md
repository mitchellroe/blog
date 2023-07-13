# Add an alias for a Bluetooth device

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
