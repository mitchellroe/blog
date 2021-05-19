# Enable Hibernate in Fedora

In Fedora 32 and 33, resuming from hibernate is not working properly. There is
[a bug](https://bugzilla.redhat.com/show_bug.cgi?id=1795422) open for this
issue, which contains a workaround.

```
sudo echo 'add_dracutmodules+=" resume " > /etc/dracut.conf.d/99-enable-resume.conf'
sudo dracut -fv
```
