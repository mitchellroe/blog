# Keychron K8 Function Keys

The function keys were not working properly on my Keychron K8 when in wired
mode. They would only function as their desktop-environment equivalent, even
when combining with the `Fn` key (e.g. F1 would _only_ decrease brightness, F2
would _only_ increase brightness, etc.).

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

## Update (as of 9 Feb 2021)

I updated the firmware on my device, and this seems to have resolved the issue
I was experiencing.
