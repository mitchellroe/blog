# Locales in Debian

I recently installed Debian 10 (buster). I was rather surprised to find there
were no languages or regional formats available in GNOME other than the one I
had chosen during installation. Debian has stopped shipping the installer with
a whole bunch of locales. You can regenerate them with `dpkg-reconfigure`.

```
# dpkg-reconfigure locales
```
