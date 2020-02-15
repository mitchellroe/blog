# Configuration for Logitech "Trackman" trackball

In `/etc/share/X11/xorg.conf.d/10-trackball.conf` for left-handed placement:

```
Section "InputClass"
    Identifier      "Marble Mouse"
    MatchProduct    "Logitech USB Trackball"
    Driver          "libinput"
    Option          "ScrollMethod" "button"
    Option          "ScrollButton" "9"
    Option          "Middle Emulation" "on"
    Option          "ButtonMapping" "3 2 1 4 5 6 7 8 9"
EndSection
```

Shout out to https://wiki.archlinux.org/index.php/Loitech_Marble_Mouse
