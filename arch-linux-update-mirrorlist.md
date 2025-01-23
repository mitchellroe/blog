# Update mirrorlist in Arch Linux

Use [`reflector`](https://wiki.archlinux.org/title/Reflector).

```
sudo systemctl enable reflector.timer
sudo systemctl start reflector.timer
```
