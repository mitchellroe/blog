# Add "drop-in" unit files in systemd

From <https://wiki.archlinux.org/title/Systemd#Drop-in_files>

To create drop-in files for the unit file <code>/usr/lib/systemd/system/<i>unit</i></code>, create the directory <code>/etc/systemd/system/<i>unit</i>.d/</code> and place *.conf* files there to override or add new options. *systemd* will parse and apply these files on top of the original unit.

The easiest way to do this is to run:
<pre>
# systemctl edit <i>unit</i> --drop-in=<i>drop_in_name</i>
</pre>

```
# systemctl edit <i>unit</i> --drop-in=<i>drop_in_name</i>
```
