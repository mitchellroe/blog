# Configure displays for GDM

From <https://wiki.archlinux.org/title/GDM#Setup_default_monitor_settings>

## 3.6 Setup default monitor settings

Some [desktop environments](https://wiki.archlinux.org/title/Desktop_environments) store display settings in `~/.config/monitors.xml`.
*xrandr* commands are then generated on the base of the file content.
GDM has a similar file stored in `/var/lib/gdm/.config/monitors.xml`.

If you have your monitors setup as you like (resolution, refresh rate, orientation, scaling, primary and so on) in `~/.config/monitors.xml` and want GDM to honor those settings:
```
# cp ~/.config/monitors.xml /var/lib/gdm/.config
```

To automatically re-configure the monitor setup on each boot, use a [drop-in file](add-drop-in-systemd-unit-files.md) for `gdm.service`:
<pre>
/etc/systemd/system/gdm.service/override.conf
<hl/>
[Service]
ExecStartPre=/bin/cp /home/<i>user</i>/.config/monitors.xml /var/lib/gdm/.config/monitors.xml
</pre>
