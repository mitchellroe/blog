# Configuration for Logitech "Trackman" trackball

## When Using X11

In `/etc/share/X11/xorg.conf.d/10-trackball.conf`, add the following for
right-handed placement:

```
Section "InputClass"
	Identifier   "Marble Mouse"
	MatchProduct "Logitech USB Trackball"
	Driver       "libinput"
	Option       "ScrollMethod"    "button"
	Option       "ScrollButton"    "8"
	Option       "MiddleEmulation" "true"
EndSection
```

For left-handed placement:

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

## When Using Wayland and GNOME

```
gsettings set org.gnome.desktop.peripherals.trackball scroll-wheel-emulation-button <button_id>
```

Thanks to <https://wiki.archlinux.org/index.php/Logitech_Marble_Mouse> for the
information.

[home](README.md)
