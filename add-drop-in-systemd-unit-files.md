# Add "drop-in" unit files in systemd

From <https://wiki.archlinux.org/title/Systemd#Drop-in_files>

## 2.3.2 Drop-in files

To create drop-in files for the unit file <code>/usr/lib/systemd/system/<i>unit</i></code>, create the directory <code>/etc/systemd/system/<i>unit</i>.d/</code> and place *.conf* files there to override or add new options. *systemd* will parse and apply these files on top of the original unit.

The easiest way to do this is to run:
<pre>
# systemctl edit <i>unit</i> --drop-in=<i>drop_in_name</i>
</pre>

This opens the file <code>/etc/systemd/system/<i>unit</i>.d/<i>drop_in_name</i>.conf</code> in your text editor (creating if necessary) and automatically reloads the unit when you are done editing. Omitting the `--drop-in=` option will result in systemd using the default file name `override.conf`.

**Note**:
- The key must be still placed in the appropriate section in the override file.
- Not all keys can be overridden with drop-in files. For example, for changing `Conflicts=` a replacement file [is necessary](https://lists.freedesktop.org/archives/systemd-devel/2017-June/038976.html).

## 2.3.3 Revert to vendor version

To revert any changes to a unit made using `systemctl edit` do:
<pre>
# systemctl revert <i>unit</i>
</pre>
