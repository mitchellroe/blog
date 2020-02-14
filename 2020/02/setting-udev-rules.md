# Configure IBM TrackPoint with udev

I recently ran into an issue while configuring my Lenovo ThinkPad X1 Carbon's
TrackPoint. It's not, by default, sensitive enough for my tastes while using
libinput. I've tried configuring it at startup with scripts that wait for the
`sensitivity` file to show up in `/sys/bus/serio/devices/serio2/` and `echo 250`
to that file. Unfortunately, this is fairly error-prone (the path can change)
and is a bit of a hack.

Another thing I've tried is to just run `xinput` when I finally get to my
desktop interface and configure it that way. Unfortunately, this requires that I
be running X, and I would prefer to use Wayland. This is also not 100%, as it
depends on when xinput was run (before or after GNOME changes its own settings).

Fortunately, I've found configuring it through udev is actually much simpler
than I though it would be. Create `/etc/udev/rules.d/10-trackpoint.rules`, and
place in it the following:

```
ACTION=="add", SUBSYSTEM=="input", ATTR{name}=="TPPS/2 IBM TrackPoint",
ATTR{device/sensitivity}="200"
```

This method allows you to configure any of the settings found in
`/sys/bus/serio/devices/serio2/` (or equivalent) by simply adding another
comma-separated statement like `ATTR{device/speed}`.

[back](/)
