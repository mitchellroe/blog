## Configure IBM TrackPoint with udev

### Why

I have run into issues with ThinkPad TrackPoints in Linux. Specifically, _there
doesn't seem to be a straight-forward way to change some of the settings
permanently_.

### What I've tried

I've tried configuring it at startup with scripts that wait for the
`sensitivity` file to show up in `/sys/bus/serio/devices/serio2/` and then `echo
250` to that file. Unfortunately, this is fairly error-prone (the path can
change) and is a bit of a hack.

Another thing I've tried is to just run `xinput` when I finally get to my
desktop interface and configure it that way. Unfortunately, this requires a)
that I be running X (I use Wayland), and b) that I do it interactively (it never
seemed to work for me as a login item).

### What I landed on

Fortunately, I've found configuring it through udev is actually much simpler
than I though it would be. Create `/etc/udev/rules.d/10-trackpoint.rules` with
the following:

```
ACTION=="add", SUBSYSTEM=="input",
ATTR{name}=="TPPS/2 IBM TrackPoint",
ATTR{device/sensitivity}="200"
```

This method allows you to configure any of the settings found in
`/sys/bus/serio/devices/serio2/` (or equivalent) by simply adding another
comma-separated statement like `ATTR{device/speed}`.

Enjoy a permanent, stable configuration of your TrackPoint.

[home](README.md)
