# Keychron K8 Function Keys

The function keys were not working properly on my Keychron K8 when in wired
mode. They would only function as their desktop-environment equivalent (i.e. F1
would _only_ decrease brightness, F2 would _only_ increase brightness, etc.),
even when combining the keystroke with the `Fn` key.

Setting the hid_apple module option of `fnmode` to 0 worked.

`/etc/modprobe.d/hid_apple.conf`:
```
options hid_apple fnmode=0
```

You can also do this ephemerally by overwriting the
`/sys/module/hid_apple/parameters/fnmode` file:
```
echo 0 > /sys/module/hid_apple/parameters/fnmode
```
